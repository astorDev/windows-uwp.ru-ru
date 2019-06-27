---
description: Ответы на часто возникающие вопросы о разработке и использовании интерфейсов API среды выполнения Windows с помощью C++/WinRT.
title: Ответы на часто задаваемые вопросы о C++/WinRT
ms.date: 04/23/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, frequently, asked, questions, faq
ms.localizationpriority: medium
ms.openlocfilehash: 914cf884b97d14af523cc61b0fcce719104783ba
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66721685"
---
# <a name="frequently-asked-questions-about-cwinrt"></a>Ответы на часто задаваемые вопросы о C++/WinRT
Ответы на часто возникающие вопросы о разработке и использовании интерфейсов API среды выполнения Windows с помощью [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt).

> [!NOTE]
> Если ваш вопрос связан с сообщением об ошибке, которое вы увидели, обратитесь также к статье [Устранение неполадок C++/WinRT](troubleshooting.md).

## <a name="how-do-i-retarget-my-cwinrt-project-to-a-later-version-of-the-windows-sdk"></a>Как изменить целевую платформу проекта C++/WinRT на более позднюю версию Windows SDK?
См. сведения в разделе [How to retarget your C++/WinRT project to a later version of the Windows SDK](news.md#how-to-retarget-your-cwinrt-project-to-a-later-version-of-the-windows-sdk) (Как изменить целевую платформу проекта C++/WinRT на более позднюю версию Windows SDK).

## <a name="why-wont-my-new-project-compile-now-that-ive-moved-to-cwinrt-20"></a>Почему мой новый проект не компилируется после перехода на C++/WinRT 2.0?
Полный набор изменений (включая критические изменения) см. в разделе [News, and changes, in C++/WinRT 2.0](news.md#news-and-changes-in-cwinrt-20) (Новости и изменения в C++/WinRT 2.0). Например, если вы используете основанный на диапазоне оператор `for` в коллекции среды выполнения Windows, то теперь нужно будет добавить `#include <winrt/Windows.Foundation.Collections.h>`.

## <a name="why-wont-my-new-project-compile-im-using-visual-studio-2017-version-1580-or-higher-and-sdk-version-17134"></a>Почему мой новый проект не компилируется? Я использую Visual Studio 2017 (версии 15.8.0 или выше) и пакет SDK версии 17134.
Если вы используете Visual Studio 2017 (версии 15.8.0 или выше) и разрабатываете проект для пакета Windows SDK версии 10.0.17134.0 (Windows 10 версии 1803), то созданный проект C++/WinRT может не скомпилироваться и вызвать *ошибку C3861 о том, что идентификатор from_abi не найден* и другие ошибки, полученные в *base.h*. Следует разрабатывать проект с более поздней (лучше соответствующей) версией Windows SDK или задать свойство проекта **C/C++**  > **Язык** > **Режим совместимости: Нет**. (Кроме того, если параметр **/permissive-** отображается в свойстве проекта **C/C++**  > **Командная строка** в разделе **Дополнительные параметры**, удалите его).

## <a name="how-do-i-resolve-the-build-error-the-cwinrt-vsix-no-longer-provides-project-build-support--please-add-a-project-reference-to-the-microsoftwindowscppwinrt-nuget-package"></a>Как устранить ошибку сборки The C++/WinRT VSIX no longer provides project build support.  Please add a project reference to the Microsoft.Windows.CppWinRT Nuget package (C++/WinRT VSIX больше не обеспечивает поддержку сборки проекта. Добавьте ссылку на пакет Nuget Microsoft.Windows.CppWinRT в проект)?
Установите пакет NuGet **Microsoft.Windows.CppWinRT** в проект. Дополнительные сведения см. в разделе [Earlier versions of the VSIX extension](intro-to-using-cpp-with-winrt.md#earlier-versions-of-the-vsix-extension) (Более ранние версии расширения VSIX).

## <a name="what-are-the-requirements-for-the-cwinrt-visual-studio-extension-vsix"></a>Каковы требования для расширения Visual Studio (VSIX) C++/WinRT?
Дополнительные сведения о версии 1.0.190128.4 расширения VSIX и более поздних версиях см. в разделе о [поддержке C++/WinRT в Visual Studio](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package). Дополнительные сведения о других версиях см. в разделе [Earlier versions of the VSIX extension](intro-to-using-cpp-with-winrt.md#earlier-versions-of-the-vsix-extension) (Более ранние версии расширения VSIX).

## <a name="whats-a-runtime-class"></a>Что такое *класс среды выполнения*?
Класс среды выполнения — это тип, который можно активировать и использовать через современные интерфейсы COM, обычно через границы исполняемого файла. Тем не менее класс среды выполнения может также использоваться в единице компиляции, которая его реализует. Класс среды выполнения объявляется в языке описания интерфейса (IDL) и может реализоваться в стандартной версии C++ с использованием C++/WinRT.

## <a name="what-do-the-projected-type-and-the-implementation-type-mean"></a>Что означают понятия *тип проекции* и *тип реализации*?
Если вы лишь *используете* класс среды выполнения Windows (класс среды выполнения), то вы имеете дело с *типом проекции*. C++/WinRT представляет собой *языковую проекцию*, поэтому типы проекции являются частью среды выполнения Windows, которая *проецируется* в C++ с помощью C++/WinRT. Дополнительные сведения см. в статье [Consume APIs with C++/WinRT](consume-apis.md) (Использование API-интерфейсов с C++/WinRT).

*Тип реализации* содержит реализацию класса среды выполнения, поэтому он доступен только в проекте, который реализует класс среды выполнения. При работе в проекте, который реализует классы среды выполнения (проекте компонента среды выполнения Windows или проекте, использующем пользовательский интерфейс XAML), очень важно понимать различия между вашим типом реализации класса среды выполнения и типом проекции, представляющим класс среды выполнения, проецируемый в C++/WinRT. Дополнительные сведения см. в разделе [Author APIs with C++/WinRT](author-apis.md) (Создание API-интерфейсов с использованием C++/WinRT).

## <a name="do-i-need-to-declare-a-constructor-in-my-runtime-classs-idl"></a>Нужно ли объявлять конструктор в IDL моего класса среды выполнения?
Только если класс среды выполнения предназначен для использования извне его единицы компиляции (это компонент среды выполнения Windows, предназначенный для общего использования клиентскими приложениями среды выполнения Windows). Дополнительные сведения о назначении и последствиях объявления конструкторов в IDL см. в разделе [Runtime class constructors](author-apis.md#runtime-class-constructors) (Конструкторы классов среды выполнения).

## <a name="why-is-the-linker-giving-me-a-lnk2019-unresolved-external-symbol-error"></a>Почему компоновщик отображает сообщение об ошибке "LNK2019: неразрешенный внешний символ"?
Если неразрешенный символ — это API из заголовков пространства имен Windows для проекции C++/WinRT (в пространстве имен **winrt**), то этот API объявляется в заголовке, который вы включили, но его определение находится в заголовке, который еще не включен. Включите заголовок с именем, соответствующим пространству имен API, и повторно выполните сборку. Дополнительные сведения см. в разделе [C++/WinRT projection headers](consume-apis.md#cwinrt-projection-headers) (Заголовки проекции C++/WinRT).

Если неразрешенный символ — это свободная функция среды выполнения Windows, такая как [RoInitialize](https://docs.microsoft.com/windows/desktop/api/roapi/nf-roapi-roinitialize), необходимо явно включить библиотеку [WindowsApp.lib](/uwp/win32-and-com/win32-apis) в проект. Проекция C++/WinRT зависит от некоторых из этих свободных (не являющихся членами) функций и точек входа. Если вы используете один из шаблонов проектов [Расширение C++/WinRT для Visual Studio (VSIX)](https://aka.ms/cppwinrt/vsix) для вашего приложения, `WindowsApp.lib` компонуется автоматически. В противном случае вы можете использовать параметры компоновки проекта, чтобы включить библиотеку, или можете сделать это в исходном коде.

```cppwinrt
#pragma comment(lib, "windowsapp")
```

Важно разрешить любые возможные ошибки компоновщика, привязав **WindowsApp.lib** вместо альтернативной библиотеки статических ссылок, иначе ваше приложение не пройдет тестирование с помощью [комплекта сертификации приложений для Windows](../debug-test-perf/windows-app-certification-kit.md), используемого Visual Studio и Microsoft Store для проверки отправок (это означает, что ваше приложение не сможет успешно попасть в Microsoft Store).

## <a name="should-i-implement-windowsfoundationiclosableuwpapiwindowsfoundationiclosable-and-if-so-how"></a>Следует ли реализовывать [**Windows::Foundation::IClosable**](/uwp/api/windows.foundation.iclosable) и если да, то каким образом?
Если у вас есть класс среды выполнения, который освобождает ресурсы в своем деструкторе, и этот класс среды предназначен для использования извне его единицы компиляции (это компонент среды выполнения Windows, предназначенный для общего использования клиентскими приложениями среды выполнения Windows), рекомендуется также реализовать **IClosable** для поддержки применения вашего класса среды выполнения языками, в которых отсутствует детерминированная финализация. Убедитесь, что ресурсы освобождаются при вызове деструктора, [**IClosable::Close**](/uwp/api/windows.foundation.iclosable.close) или их обоих. **IClosable::Close** можно вызывать произвольное число раз.

## <a name="do-i-need-to-call-iclosablecloseuwpapiwindowsfoundationiclosableclose-on-runtime-classes-that-i-consume"></a>Нужно ли вызывать [**IClosable::Close**](/uwp/api/windows.foundation.iclosable.close) для классов среды выполнения, которые я использую?
**IClosable** существует для поддержки языков, в которых отсутствует детерминированная финализация. Таким образом, не следует вызывать метод **IClosable::Close** из C++/WinRT, за исключением очень редких случаев состязания завершения работы или полувзаимной блокировки. К примеру, если вы используете типы **Windows.UI.Composition**, может возникнуть ситуация, в которой вам понадобится удалить объекты в установленной последовательности, а не оставлять задачу их уничтожения программе-оболочке C++/WinRT.

## <a name="can-i-use-llvmclang-to-compile-with-cwinrt"></a>Можно ли использовать LLVM/Clang для компиляции с C++/WinRT?
Мы не поддерживаем цепочку инструментов LLVM и Clang для C++/WinRT, но мы используем ее для внутренней проверки соответствия стандартам C++/WinRT. Например, если вы хотите имитировать то, что мы делаем внутри, вы можете поэкспериментировать, как описано ниже.

Перейдите на [страницу загрузки LLVM](https://releases.llvm.org/download.html), найдите пункт **Download LLVM 6.0.0** (Скачать LLVM 6.0.0) > **Pre-Built Binaries** (Стандартные двоичные файлы) и скачайте **Clang для Windows (64-разрядная версия)** . Во время установки добавьте LLVM в системную переменную PATH, чтобы вызывать LLVM из командной строки. В целях этого эксперимента можно пропустить любые ошибки Failed to find MSBuild toolsets directory (Не удалось найти каталог цепочки инструментов MSBuild) и MSVC integration install failed (Не удалось установить интеграцию MSVC), если они отображаются. Существует множество способов вызова LLVM/Clang. В примере ниже показан только один из них.

```cmd
C:\ExperimentWithLLVMClang>type main.cpp
// main.cpp
#pragma comment(lib, "windowsapp")
#pragma comment(lib, "ole32")

#include <winrt/Windows.Foundation.h>
#include <stdio.h>
#include <iostream>

using namespace winrt;

int main()
{
    winrt::init_apartment();
    Windows::Foundation::Uri rssFeedUri{ L"https://blogs.windows.com/feed" };
    std::wcout << rssFeedUri.Domain().c_str() << std::endl;
}

C:\ExperimentWithLLVMClang>clang-cl main.cpp /EHsc /I ..\.. -Xclang -std=c++17 -Xclang -Wno-delete-non-virtual-dtor -o app.exe

C:\ExperimentWithLLVMClang>app
windows.com
```

Так как C++/WinRT использует функции стандарта C++ 17, вам потребуются все флаги компилятора, необходимые для обеспечения такой поддержки. Эти флаги в разных компиляторах отличаются.

Visual Studio — это средство разработки, которое мы поддерживаем и рекомендуем для C++/WinRT. См. сведения в разделе о [поддержке C++/WinRT в Visual Studio](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

## <a name="why-doesnt-the-generated-implementation-function-for-a-read-only-property-have-the-const-qualifier"></a>Почему у созданной функции реализации для свойства только для чтения нет квалификатора `const`?
Когда вы объявляете свойство только для чтения в [MIDL 3.0](/uwp/midl-3/), вы можете ожидать, что средство `cppwinrt.exe` создаст для вас функцию реализации, которая квалифицирована `const` (константная функция обрабатывает *этот* указатель как константу).

Мы рекомендуем использовать константу везде, где это возможно, но само средство `cppwinrt.exe` не пытается выяснить, какие функции реализации могут быть константой, а какие нет. Вы можете сделать любую из ваших функций реализации константной, как в этом примере.

```cppwinrt
struct MyStringable : winrt::implements<MyStringable, winrt::Windows::Foundation::IStringable>
{
    winrt::hstring ToString() const
    {
        return L"MyStringable";
    }
};
```

Вы можете удалить этот квалификатор `const` в **ToString**, если решите, что вам нужно изменить некоторое состояние объекта в его реализации. При этом сделайте так, чтобы каждая из ваших функций-членов была константной или не константной. Другими словами, не перегружайте функцию реализации в `const`.

Помимо функций реализации константы используются в проекции функций среды выполнения Windows. Рассмотрим следующий код.

```cppwinrt
int main()
{
    winrt::Windows::Foundation::IStringable s{ winrt::make<MyStringable>() };
    auto result{ s.ToString() };
}
```

Чтобы вызвать **ToString** выше, команда **Go To Declaration** в Visual Studio показывает, что проекция среды выполнения Windows **IStringable::ToString** в C++/WinRT выглядит следующим образом.

```cppwinrt
winrt::hstring ToString() const;
```

Функции в проекции являются константами, независимо от того, как вы решите их реализовать. На самом деле проекция вызывает двоичный интерфейс приложения (ABI), который представляет собой вызов с помощью указателя интерфейса COM. Единственное состояние, с которым взаимодействует спроецированная функция **ToString**, — это указатель интерфейса COM. Функция является константой, поэтому нет необходимости изменять этот указатель. Это гарантирует, что функция не изменит ссылку **IStringable**, с помощью которой вы выполняете вызов, а также гарантирует, что вы можете вызывать **ToString** даже с константной ссылкой на **IStringable**.

Эти примеры `const` предоставляют подробные сведения о реализации проекций и реализаций C++/WinRT и позволяют создавать аккуратный код. В COM и в интерфейсе ABI среды выполнения Windows (для функций-членов) нет такого элемента, как `const`.

## <a name="do-you-have-any-recommendations-for-decreasing-the-code-size-for-cwinrt-binaries"></a>У вас есть рекомендации по уменьшению размера кода для двоичных файлов C++/WinRT?
При работе с объектами среды выполнения Windows следует избегать шаблона кодирования, показанного ниже, так как он может оказать негативное влияние на ваше приложение по причине формирования большего количества двоичного кода, чем это необходимо.

```cppwinrt
anobject.b().c().d();
anobject.b().c().e();
anobject.b().c().f();
```

В среде выполнения Windows компилятор не может кэшировать ни значение `c()`, ни интерфейсы для каждого метода, вызываемого через косвенное обращение ("."). Если не вмешаться в процесс, это приведет к увеличению количества виртуальных вызовов и подсчета ссылок. Приведенный выше шаблон может легко создать вдвое больше кода, чем строго необходимо. Вместо этого по возможности используйте показанный ниже шаблон. Он создает намного меньше кода и может также значительно повысить производительность во время выполнения.

```cppwinrt
auto a{ anobject.b().c() };
a.d();
a.e();
a.f();
```

Приведенный выше рекомендуемый шаблон применим не только к C++/WinRT, но и ко всем проекциям языка среды выполнения Windows.

## <a name="how-do-i-turn-a-string-into-a-typemdashfor-navigation-for-example"></a>Как превратить строку в какой-либо тип, например для навигации?
В конце [примера кода представления навигации](/windows/uwp/design/controls-and-patterns/navigationview#code-example) (в основном на C#) приведен фрагмент кода C++/WinRT, показывающий, как это можно сделать.

> [!NOTE]
> Если в этой статье вы не нашли ответы на свои вопросы, можете перейти к сообществу разработчиков[Visual Studio C++](https://developercommunity.visualstudio.com/spaces/62/index.html) или воспользоваться тегом [`c++-winrt` на сайте Stack Overflow](https://stackoverflow.com/questions/tagged/c%2b%2b-winrt).
