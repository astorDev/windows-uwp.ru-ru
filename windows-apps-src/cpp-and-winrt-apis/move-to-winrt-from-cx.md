---
author: stevewhims
description: В этом разделе объясняется, как перенести код C++/CX в его эквивалент на C++/WinRT.
title: Переход на C++/WinRT из C++/CX
ms.author: stwhi
ms.date: 07/20/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, перенос, C++/CX
ms.localizationpriority: medium
ms.openlocfilehash: ac7affb044c6b60a249b154cc62379c7517161b0
ms.sourcegitcommit: 00d27738325d6db5b5e481911ae7fac0711b05eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "3664999"
---
# <a name="move-to-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt-from-ccx"></a>Переход на [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) из C++/CX
В это разделе объясняется, как перенести код [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx) в его эквивалент на C++/WinRT.

> [!IMPORTANT]
> Если вы хотите перенести постепенно вашего [C + +/ CX](/cpp/cppcx/visual-c-language-reference-c-cx) кода на C + +/ WinRT, то можете. C + +/ CX и C + +/ WinRT кода могут сосуществовать в одном проекте, за исключением поддержку компилятора XAML и компоненты среды выполнения Windows. Для этих исключений, вам потребуется выбрать либо C + +/ CX или C + +/ WinRT в пределах одного и того же проекта. Однако вы можете использовать компонент среды выполнения Windows на коэффициент код за пределы вашего приложения на XAML, как его переноса. Либо переместите столько C + +/ CX кода, как и в компоненте и затем измените XAML проекта на C + +/ WinRT. Или в противном случае оставьте проект XAML как C + +/ CX, создайте новый C + +/ компонента WinRT и переносом C + +/ CX кода из проекта XAML и в компонент. Также возможны C + +/ CX проект компонента вместе с C + +/ WinRT проект компонента в одном решении, оба из них ссылки из проекта приложения и постепенно порта от одного к другому.

> [!NOTE]
> [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx) и Windows SDK объявляют типы в корневом пространстве имен **Windows**. Тип Windows, проецируемый в C++/WinRT, имеет то же полное имя, что и тип Windows, но он размещается в пространстве имен C++ **winrt**. Эти различные пространства имен позволяют выполнять перенос из C++/CX в C++/WinRT в удобном вам темпе.

Имея в виду упомянутые выше исключения, первый шаг в перенос в проект C + +/ WinRT — вручную добавить C + +/ WinRT поддержки (см. в разделе [Поддержка Visual Studio для C + +/ WinRT и VSIX](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix)). Чтобы это сделать, измените файл `.vcxproj`, найдите `<PropertyGroup Label="Globals">` и внутри этой группы свойств настройте свойство `<CppWinRTEnabled>true</CppWinRTEnabled>`. Одним из последствий этого изменения является отключение поддержки для C++/CX в проекте. Рекомендуется оставить поддержку отключенной, чтобы сообщения о построении помогут вам найти (и порт) все зависимости на C + +/ CX. либо можно включить поддержку (в свойствах проекта **C/C++** \> **Общие** \> **потребления среды выполнения Windows Расширение** \> **Да (/ZW)**) и постепенно порта.

Задайте свойству проекта **Общие** \> **Версия целевой платформы** значение 10.0.17134.0 (Windows 10, версия 1803) или более позднюю версию.

В предварительно скомпилированный файл заголовка (обычно это `pch.h`) добавьте `winrt/base.h`.

```cppwinrt
#include <winrt/base.h>
```

При добавлении заголовков с API-интерфейсами Windows, проецируемыми в C++/WinRT, (например, `winrt/Windows.Foundation.h`) необходимость в явном добавлении `winrt/base.h` подобным образом отсутствует, так как этот элемент будет добавлен автоматически.

Если в проекте также используются типы [библиотеки шаблонов C++ для среды выполнения Windows (WRL)](/cpp/windows/windows-runtime-cpp-template-library-wrl), см. раздел [Перенос кода в C + +/ WinRT из WRL](move-to-winrt-from-wrl.md).

## <a name="parameter-passing"></a>Передача параметров
При написании исходного кода на C++/CX вы передаете типы C++/CX в качестве параметров функции как ссылки циркумфлекса (\^).

```cpp
void LogPresenceRecord(PresenceRecord^ record);
```

В C++/WinRT для синхронных функций следует использовать параметры `const&` по умолчанию. Это позволит избежать создания копий и заблокированных служебных данных. Однако ваши сопрограммы должны использовать передачу параметров по значению, чтобы захват параметров осуществлялся по значению и чтобы избежать проблем с жизненным циклом (дополнительные сведения см. в разделе [Параллельная обработка и асинхронные операции с помощью C++/WinRT](concurrency.md)).

```cppwinrt
void LogPresenceRecord(PresenceRecord const& record);
IASyncAction LogPresenceRecordAsync(PresenceRecord const record);
```

Объект ++/WinRT по сути является значением, содержащим указатель интерфейса на резервный объект среды выполнения Windows. При копировании объекта C++/WinRT компилятор копирует инкапсулированный указатель интерфейса, увеличивая его счетчик ссылок. Последующее уничтожение копии подразумевает уменьшение счетчика ссылок. Таким образом, утрачиваются только служебные данные копии при необходимости.

## <a name="variable-and-field-references"></a>Ссылки переменных и полей
При написании исходного кода C++/CX для установки ссылок на объекты среды выполнения Windows следует использовать переменные со знаком циркумфлекса (\^) и оператор косвенного обращения (-&gt;) для разыменовывания переменной с циркумфлексом.

```cpp
IVectorView<User^>^ userList = User::Users;

if (userList != nullptr)
{
    for (UINT32 iUser = 0; iUser < userList->Size; ++iUser)
    ...
```

При переносе в эквивалентный C + +/ WinRT кода, по сути вы удаляете циркумфлексы и изменяете оператор косвенного (-&gt;) оператор-точку (.), так как C + +/ WinRT проецируемые типы являются значениями, а не указателями.

```cppwinrt
IVectorView<User> userList = User::Users();

if (userList != nullptr)
{
    for (UINT32 iUser = 0; iUser < userList.Size(); ++iUser)
    ...
```

## <a name="properties"></a>Свойства
Расширения языка C++/CX включают концепцию свойств. При написании исходного кода C++/CX к свойству можно получить доступ, как если бы оно было полем. В стандартной версии C++ отсутствует концепция свойств, поэтому в C++/WinRT осуществляется вызов функций get и set.

В примерах ниже **XboxUserId**, **UserState**, **PresenceDeviceRecords** и **Size** являются свойствами.

### <a name="retrieving-a-value-from-a-property"></a>Извлечение значения из свойства
Вот как получить значение свойства в C++/CX.

```cpp
void Sample::LogPresenceRecord(PresenceRecord^ record)
{
    auto id = record->XboxUserId;
    auto state = record->UserState;
    auto size = record->PresenceDeviceRecords->Size;
}
```

Эквивалентный исходный код C++/WinRT вызывает функцию с тем же именем, что и свойство, но без параметров.

```cppwinrt
void Sample::LogPresenceRecord(PresenceRecord const& record)
{
    auto id = record.XboxUserId();
    auto state = record.UserState();
    auto size = record.PresenceDeviceRecords().Size();
}
```

Обратите внимание, что **PresenceDeviceRecords** возвращает объект среды выполнения Windows, у которого, в свою очередь, есть функция **Size**. Так как возвращенный объект также является проецируемым типом C++/WinRT, необходимо выполнить разыменование, используя оператор-точку для вызова **Size **.

### <a name="setting-a-property-to-a-new-value"></a>Задание свойству нового значения
Задание свойству нового значения выполняется по аналогичной схеме. Во-первых, это делается в C++/CX.

```cpp
record->UserState = newValue;
```

Чтобы реализовать эквивалент в C++/WinRT, вызовите функцию с тем же именем, что и у свойства, а затем передайте аргумент.

```cppwinrt
record.UserState(newValue);
```

## <a name="creating-an-instance-of-a-class"></a>Создание экземпляра класса
Работа с объектом C++/CX осуществляется с помощью его дескриптора, так называемой ссылки со знаком циркумфлекс (\^). Создание нового объекта выполняется с помощью ключевого слова `ref new`, которое, в свою очередь, вызывает [**RoActivateInstance**](https://msdn.microsoft.com/library/br224646), чтобы активировать новый экземпляр класса среды выполнения.

```cpp
using namespace Windows::Storage::Streams;

class Sample
{
private:
    Buffer^ m_gamerPicBuffer = ref new Buffer(MAX_IMAGE_SIZE);
};
```

Объект C++/WinRT представляет собой значение; поэтому его можно разместить в стеке или в качестве поля объекта. Для размещения объекта C++/WinRT *никогда* не следует использовать `ref new` (или `new`). **RoActivateInstance** продолжает вызываться в фоновом режиме.

```cppwinrt
using namespace winrt::Windows::Storage::Streams;

struct Sample
{
private:
    Buffer m_gamerPicBuffer{ MAX_IMAGE_SIZE };
};
```

Если инициализация ресурса является затратной, рекомендуется отложить инициализацию до того момента, когда возникнет фактическая необходимость в ресурсе.

```cpp
using namespace Windows::Storage::Streams;

class Sample
{
public:
    void DelayedInit()
    {
        // Allocate the actual buffer.
        m_gamerPicBuffer = ref new Buffer(MAX_IMAGE_SIZE);
    }

private:
    Buffer^ m_gamerPicBuffer;
};
```

Тот же код переносится в C++/WinRT. Обратите внимание на особенности использования конструктора `nullptr`. Дополнительные сведения об этом конструкторе см. в разделе [Использование API-интерфейсов с помощью C++/WinRT](consume-apis.md).

```cppwinrt
using namespace winrt::Windows::Storage::Streams;

struct Sample
{
    void DelayedInit()
    {
        // Allocate the actual buffer.
        m_gamerPicBuffer = Buffer(MAX_IMAGE_SIZE);
    }

private:
    Buffer m_gamerPicBuffer{ nullptr };
};
```

## <a name="converting-from-a-base-runtime-class-to-a-derived-one"></a>Преобразование из класса среды выполнения базового производного один
Это обычно существует ссылка для базового, вы знаете, что ссылается на объект производного типа. В C + +/ CX, можно использовать `dynamic_cast` к *приведите* ссылку для базового в ссылки к производным. `dynamic_cast` — Это лишь скрытые вызов [**QueryInterface**](https://msdn.microsoft.com/library/windows/desktop/ms682521). Ниже приведен стандартный пример&mdash;обрабатывать событие изменения свойства зависимостей, и вы хотите приведение **DependencyObject** вернуться к фактического типа, которому принадлежит свойство зависимостей.

```cpp
void BgLabelControl::OnLabelChanged(Windows::UI::Xaml::DependencyObject^ d, Windows::UI::Xaml::DependencyPropertyChangedEventArgs^ e)
{
    BgLabelControl^ theControl{ dynamic_cast<BgLabelControl^>(d) };

    if (theControl != nullptr)
    {
        // succeeded ...
    }
}
```

Эквивалент C + +/ WinRT код заменяет `dynamic_cast` с помощью вызова функции [**IUnknown::try_as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntryas-function) , который инкапсулирует **QueryInterface**. У вас также есть возможность вместо этого вызовите метод [**IUnknown::as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function), который создает исключение, если запрашиваются необходимый интерфейс (интерфейс по умолчанию для типа, который запрашивается) не возвращается. Вот C + +/ WinRT пример кода.

```cppwinrt
void BgLabelControl::OnLabelChanged(Windows::UI::Xaml::DependencyObject const& d, Windows::UI::Xaml::DependencyPropertyChangedEventArgs const& e)
{
    if (BgLabelControlApp::BgLabelControl theControl{ d.try_as<BgLabelControlApp::BgLabelControl>() })
    {
        // succeeded ...
    }

    try
    {
        BgLabelControlApp::BgLabelControl theControl{ d.as<BgLabelControlApp::BgLabelControl>() };
        // succeeded ...
    }
    catch (winrt::hresult_no_interface const&)
    {
        // failed ...
    }
}
```

## <a name="event-handling-with-a-delegate"></a>Обработка событий с помощью делегата
Ниже приведен стандартный пример обработки события в C++/CX с помощью лямбда-функции в качестве делегата в этом случае.

```cpp
auto token = myButton->Click += ref new RoutedEventHandler([&](Platform::Object^ sender, RoutedEventArgs^ args)
{
    // Handle the event.
});
```

Это эквивалентно и для C++/WinRT.

```cppwinrt
auto token = myButton().Click([&](IInspectable const& sender, RoutedEventArgs const& args)
{
    // Handle the event.
});
```

Вместо лямбда-функции вы можете реализовать делегат в качестве свободной функции или в качестве указателя на функцию-член. Дополнительные сведения см. в разделе [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md).

При переносе из кода на C++/CX, где события и делегаты используются для внутренних целей (а не в разных двоичных файлах) [**winrt::delegate**](/uwp/cpp-ref-for-winrt/delegate) поможет вам выполнить репликацию этой модели в C++/WinRT. См. также [параметризованные делегаты, простой сигналы и обратные вызовы в проекте](author-events.md#parameterized-delegates-simple-signals-and-callbacks-within-a-project).

## <a name="revoking-a-delegate"></a>Отзыв делегата
В C++/CX можно использовать оператор `-=`, чтобы отозвать регистрацию предыдущих событий.

```cpp
myButton->Click -= token;
```

Это эквивалентно и для C++/WinRT.

```cppwinrt
myButton().Click(token);
```

Дополнительные сведения и описание параметров см. в разделе [Отзыв зарегистрированного делегата](handle-events.md#revoke-a-registered-delegate).

## <a name="mapping-ccx-platform-types-to-cwinrt-types"></a>Сопоставление типов **Platform** в C++/CX с типами C++/WinRT
C++/CX предоставляет несколько типов данных в пространстве имен **Platform**. Эти типы не являются типичными для C++, поэтому их можно использовать только при включении расширений языка среды выполнения Windows (Свойство проекта Visual Studio **C/C++** > **Общие** > **Использовать расширение среды выполнения Windows** > **Да (/ZW)**). В таблице ниже приведены инструкции по переносу из типов пространства имен **Platform** в их эквиваленты в C++/WinRT. После выполнения переноса, так как C++/WinRT является стандартной версией C++, параметр `/ZW` можно отключить.

| C++/CX | C++/WinRT |
| ---- | ---- |
| **Платформа:: Agile\ ^** | [**WinRT::agile_ref**](/uwp/cpp-ref-for-winrt/agile-ref) |
| **Platform::Exception\^** | [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error) |
| **Platform::InvalidArgumentException\^** | [**winrt::hresult_invalid_argument**](/uwp/cpp-ref-for-winrt/error-handling/hresult-invalid-argument) |
| **Platform::Object\^** | **winrt::Windows::Foundation::IInspectable** |
| **Platform::String\^** | [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring) |

### <a name="port-platformagile-to-winrtagileref"></a>Порт **платформы:: Agile\ ^** для **winrt::agile_ref**
**Платформы:: Agile\ ^** типа в C + +/ CX представляет класс среды выполнения Windows, который может осуществляться из любого потока. C + +/ WinRT эквивалентом является [**winrt::agile_ref**](/uwp/cpp-ref-for-winrt/agile-ref).

В C++/CX.

```cpp
Platform::Agile<Windows::UI::Core::CoreWindow> m_window;
```

В C++/WinRT.

```cppwinrt
winrt::agile_ref<Windows::UI::Core::CoreWindow> m_window;
```

### <a name="port-platformexception-to-winrthresulterror"></a>Перенесите **Platform::Exception\^** в **winrt::hresult_error**
Когда API-интерфейс среды выполнения Windows возвращает результат, отличный от S\_OK HRESULT, в C++/CX создается тип **Platform::Exception\^**. Для C++/WinRT эквивалентом является [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error).

Чтобы выполнить перенос в C++/WinRT, внесите изменения в код так, чтобы вместо **Platform::Exception\^** использовался метод **winrt::hresult_error**.

В C++/CX.

```cpp
catch (Platform::Exception^ ex)
```

В C++/WinRT.

```cppwinrt
catch (winrt::hresult_error const& ex)
```

C++/WinRT предоставляет эти классы исключений.

| Тип исключения | Базовый класс | HRESULT |
| ---- | ---- | ---- |
| [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error) | | Вызовите метод [**hresult_error::to_abi**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error#hresulterrortoabi-function) |
| [**winrt::hresult_access_denied**](/uwp/cpp-ref-for-winrt/error-handling/hresult-access-denied) | **winrt::hresult_error** | E_ACCESSDENIED |
| [**winrt::hresult_canceled**](/uwp/cpp-ref-for-winrt/error-handling/hresult-canceled) | **winrt::hresult_error** | ERROR_CANCELLED |
| [**winrt::hresult_changed_state**](/uwp/cpp-ref-for-winrt/error-handling/hresult-changed-state) | **winrt::hresult_error** | E_CHANGED_STATE |
| [**winrt::hresult_class_not_available**](/uwp/cpp-ref-for-winrt/error-handling/hresult-class-not-available) | **winrt::hresult_error** | CLASS_E_CLASSNOTAVAILABLE |
| [**winrt::hresult_illegal_delegate_assignment**](/uwp/cpp-ref-for-winrt/error-handling/hresult-illegal-delegate-assignment) | **winrt::hresult_error** | E_ILLEGAL_DELEGATE_ASSIGNMENT |
| [**winrt::hresult_illegal_method_call**](/uwp/cpp-ref-for-winrt/error-handling/hresult-illegal-method-call) | **winrt::hresult_error** | E_ILLEGAL_METHOD_CALL |
| [**winrt::hresult_illegal_state_change**](/uwp/cpp-ref-for-winrt/error-handling/hresult-illegal-state-change) | **winrt::hresult_error** | E_ILLEGAL_STATE_CHANGE |
| [**winrt::hresult_invalid_argument**](/uwp/cpp-ref-for-winrt/error-handling/hresult-invalid-argument) | **winrt::hresult_error** | E_INVALIDARG |
| [**winrt::hresult_no_interface**](/uwp/cpp-ref-for-winrt/error-handling/hresult-no-interface) | **winrt::hresult_error** | E_NOINTERFACE |
| [**winrt::hresult_not_implemented**](/uwp/cpp-ref-for-winrt/error-handling/hresult-not-implemented) | **winrt::hresult_error** | E_NOTIMPL |
| [**winrt::hresult_out_of_bounds**](/uwp/cpp-ref-for-winrt/error-handling/hresult-out-of-bounds) | **winrt::hresult_error** | E_BOUNDS |
| [**winrt::hresult_wrong_thread**](/uwp/cpp-ref-for-winrt/error-handling/hresult-wrong-thread) | **winrt::hresult_error** | RPC_E_WRONG_THREAD |

Обратите внимание, что каждый класс (через базовый класс **hresult_error**) предоставляет функцию [**to_abi**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error#hresulterrortoabi-function), которая возвращает HRESULT ошибки, и функцию [**message**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error#hresulterrormessage-function), которая возвращает строковое представление HRESULT.

Ниже приведен пример создания исключения в C++/CX.

```cpp
throw ref new Platform::InvalidArgumentException(L"A valid User is required");
```

А также эквивалент в C++/WinRT.

```cppwinrt
throw winrt::hresult_invalid_argument{ L"A valid User is required" };
```

### <a name="port-platformobject-to-winrtwindowsfoundationiinspectable"></a>Перенесите **Platform::Object\^** в **winrt::Windows::Foundation::IInspectable**
Как и все типы C++/WinRT, **winrt::Windows::Foundation::IInspectable** —это тип значения. Ниже описано, как инициализировать переменную этого типа в значение null.

```cppwinrt
winrt::Windows::Foundation::IInspectable var{ nullptr };
```

### <a name="port-platformstring-to-winrthstring"></a>Перенесите **Platform::String\^** в **winrt::hstring**
**Platform::String\^** является эквивалентом типа HSTRING ABI среды выполнения Windows. Для C++/WinRT эквивалентом является [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring). Однако с помощью C++/WinRT можно вызывать API среды выполнения Windows, используя типы широких строк стандартной библиотеки C++, такие как **std::wstring**, и/или литералы широких строк. Дополнительные сведения и примеры кода см. в разделе [Обработка строк в C++/WinRT](strings.md).

С помощью C++/CX можно получить доступ к свойству [**Platform::String::Data**](https://docs.microsoft.com/en-us/cpp/cppcx/platform-string-class#data), чтобы получить строку в качестве массива **const wchar_t\**** языков группы C (например, чтобы передать ее в **std::wcout**).

```C++
auto var = titleRecord->TitleName->Data();
```

Чтобы сделать то же самое в C++/WinRT, можно использовать функцию [**hstring::c_str**](/uwp/api/windows.foundation.uri#hstringcstr-function), чтобы получить версию строки, заканчивающуюся символом NULL (языка группы С), так же как и из **std::wstring**.

```C++
auto var = titleRecord.TitleName().c_str();
```

При необходимости реализации API-интерфейсов, получающих или возвращающих строки, как правило, требуется изменить любой код C++/CX, использующий **Platform::String\^**, чтобы вместо этого применить **winrt::hstring**.

Вот пример API-интерфейса C++/CX, который принимает строковое значение.

```cpp
void LogWrapLine(Platform::String^ str);
```

Для C++/WinRT этот API-интерфейс можно объявить таким же образом в [MIDL 3.0](/uwp/midl-3).

```idl
// LogType.idl
void LogWrapLine(String str);
```

Затем цепочка инструментов C++/WinRT создаст исходный код, который будет выглядеть следующим образом.

```cppwinrt
void LogWrapLine(winrt::hstring const& str);
```

## <a name="important-apis"></a>Важные API
* [winrt::delegate struct template](/uwp/cpp-ref-for-winrt/delegate)
* [winrt::hresult_error struct](/uwp/cpp-ref-for-winrt/error-handling/hresult-error)
* [winrt::hstring struct](/uwp/cpp-ref-for-winrt/hstring)
* [пространство имен WinRT](/uwp/cpp-ref-for-winrt/winrt)

## <a name="related-topics"></a>Статьи по теме
* [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx)
* [Создание событий в C++/WinRT](author-events.md)
* [Параллельная обработка и асинхронные операции с помощью C++/WinRT](concurrency.md)
* [Использование API-интерфейсов с помощью C++/WinRT](consume-apis.md)
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)
* [Справочник по языку Microsoft Interface Definition Language 3.0](/uwp/midl-3)
* [Переход на C++/WinRT с WRL](move-to-winrt-from-wrl.md)
* [Обработка строк в C++/WinRT](strings.md)
