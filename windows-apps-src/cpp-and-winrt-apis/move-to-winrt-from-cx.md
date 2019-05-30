---
description: В этом разделе объясняется, как перенести код C++/CX в его эквивалент на C++/WinRT.
title: Переход на C++/WinRT с C++/CX
ms.date: 01/17/2019
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, перенос, C++/CX
ms.localizationpriority: medium
ms.openlocfilehash: 7fbe10e41da1b330d6f5042bea109a8a0e04f8ad
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66360161"
---
# <a name="move-to-cwinrt-from-ccx"></a>Переход на C++/WinRT с C++/CX

В этом разделе показано, как перенести код в [ C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx) проекта в его эквивалент в [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt).

## <a name="porting-strategies"></a>Стратегии переноса

Если вы хотите постепенно порт вашей C++/CX код, чтобы C++/WinRT, попробуйте выполнить. C++/CX и C++/WinRT кода могут сосуществовать в одном проекте, за исключением поддержки компилятора XAML и компоненты среды выполнения Windows. Для этих двух исключений, необходимо ориентироваться C++/CX или C++/WinRT внутри одного проекта.

> [!IMPORTANT]
> Если проект создается в приложении XAML, то один рабочий процесс, мы рекомендуем сначала создать новый проект в Visual Studio, с помощью одного из C++/WinRT шаблоны проектов (см. в разделе [поддержка Visual Studio C++/WinRT](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package)). Затем, с которого начинается копирование исходного кода и разметки через C++/CX проекта. Вы можете добавить новые страницы XAML с **проекта** \> **добавить новый элемент...** \> **Visual C++**   >  **пустая страница (C++/WinRT)** .
>
> Кроме того, можно использовать в компонент среды выполнения Windows для идентификации кода из XAML C++/CX проект как портировать его. Либо переместите столько C++/CX кода, как и в компонент и затем изменить проект XAML C++/WinRT. Или в противном случае оставьте проект XAML как C++/CX, создайте новый C++/WinRT компонента и начать перенос C++/CX код из проекта XAML в компонент. Также имеется C++/CX проект компонента вместе с C++/проект компонента WinRT в одном решении, ссылаться на них из проекта приложения и постепенно порта от одного к другому. См. в разделе [взаимодействия между C++/WinRT и C++/CX](interop-winrt-cx.md) Дополнительные сведения об использовании двух языковых проекций в том же проекте.

> [!NOTE]
> [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx) и Windows SDK объявляют типы в корневом пространстве имен **Windows**. Тип Windows, проецируемый в C++/WinRT, имеет то же полное имя, что и тип Windows, но он размещается в пространстве имен C++ **winrt**. Эти различные пространства имен позволяют выполнять перенос из C++/CX в C++/WinRT в удобном вам темпе.

Памятуя о том, упомянутые выше исключения, что первым этапом перенос C++/CX проект, чтобы C++/WinRT — Ручное добавление C++поддержки /WinRT к нему (см. в разделе [поддержка Visual Studio C++/WinRT](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package)). Чтобы сделать это, установите [пакет Microsoft.Windows.CppWinRT NuGet](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) в проект. Откройте проект в Visual Studio щелкните **проекта** \> **управление пакетами NuGet...** \> **Обзор**введите или вставьте **Microsoft.Windows.CppWinRT** в поле поиска, выберите элемент в результатах поиска, а затем нажмите кнопку **установить** для установки пакета для этого проекта. Одним из последствий этого изменения является отключение поддержки для C++/CX в проекте. Рекомендуется оставить отключена, чтобы сообщения о построении вы могли находить (и порт) поддержка все зависимости на C++/CX. также можно включить поддержку обратно на (в свойствах проекта **C /C++**  \>  **Общие** \> **использовать расширение среды выполнения Windows** \> **Да (/ZW)** ) и постепенно порта.

Убедитесь, это свойство проекта **Общие** \> **версии целевой платформы** присваивается 10.0.17134.0 (Windows 10 версии 1803) или более поздней версии.

В предварительно скомпилированный файл заголовка (обычно это `pch.h`) добавьте `winrt/base.h`.

```cppwinrt
#include <winrt/base.h>
```

При добавлении заголовков с API-интерфейсами Windows, проецируемыми в C++/WinRT, (например, `winrt/Windows.Foundation.h`) необходимость в явном добавлении `winrt/base.h` подобным образом отсутствует, так как этот элемент будет добавлен автоматически.

Если в проекте также используются типы [библиотеки шаблонов C++ для среды выполнения Windows (WRL)](/cpp/windows/windows-runtime-cpp-template-library-wrl), см. раздел [Перенос кода в C + +/ WinRT из WRL](move-to-winrt-from-wrl.md).

## <a name="parameter-passing"></a>Передача параметров
При написании C++/CX исходный код, передайте C++/CX типов как параметры функции как hat (\^) ссылки.

```cppcx
void LogPresenceRecord(PresenceRecord^ record);
```

В C++/WinRT для синхронных функций следует использовать параметры `const&` по умолчанию. Это позволит избежать создания копий и заблокированных служебных данных. Однако ваши сопрограммы должны использовать передачу параметров по значению, чтобы захват параметров осуществлялся по значению и чтобы избежать проблем с жизненным циклом (дополнительные сведения см. в разделе [Параллельная обработка и асинхронные операции с помощью C++/WinRT](concurrency.md)).

```cppwinrt
void LogPresenceRecord(PresenceRecord const& record);
IASyncAction LogPresenceRecordAsync(PresenceRecord const record);
```

Объект ++/WinRT по сути является значением, содержащим указатель интерфейса на резервный объект среды выполнения Windows. При копировании объекта C++/WinRT компилятор копирует инкапсулированный указатель интерфейса, увеличивая его счетчик ссылок. Последующее уничтожение копии подразумевает уменьшение счетчика ссылок. Таким образом, утрачиваются только служебные данные копии при необходимости.

## <a name="variable-and-field-references"></a>Ссылки переменных и полей
При написании C++/CX исходного кода, используйте hat (\^) переменные для ссылки на объекты среды выполнения Windows и стрелку (-&gt;) оператор для разыменования hat переменной.

```cppcx
IVectorView<User^>^ userList = User::Users;

if (userList != nullptr)
{
    for (UINT32 iUser = 0; iUser < userList->Size; ++iUser)
    ...
```

При переносе в эквивалентную C++/WinRT кода, можно получить долгий путь, удалив шляпы и изменение оператора стрелки (-&gt;) на оператор-точка (.). C++/ WinRT проецировать типы являются значения, а не указатель.

```cppwinrt
IVectorView<User> userList = User::Users();

if (userList != nullptr)
{
    for (UINT32 iUser = 0; iUser < userList.Size(); ++iUser)
    ...
```

Конструктор по умолчанию для C++указатель hat /CX ей присваивается значение null. Вот C++/CX пример кода, в котором мы создадим переменную или поле правильного типа, но тот, который не инициализирована. Другими словами, он не относится изначально **TextBlock**; мы намерены ссылка будет назначить позже.

```cppcx
TextBlock^ textBlock;

class MyClass
{
    TextBlock^ textBlock;
};
```

Для его эквивалента в C++/WinRT, см. в разделе [инициализации Delayed](consume-apis.md#delayed-initialization).

## <a name="properties"></a>Свойства
Расширения языка C++/CX включают концепцию свойств. При написании исходного кода C++/CX к свойству можно получить доступ, как если бы оно было полем. В стандартной версии C++ отсутствует концепция свойств, поэтому в C++/WinRT осуществляется вызов функций get и set.

В примерах ниже **XboxUserId**, **UserState**, **PresenceDeviceRecords** и **Size** являются свойствами.

### <a name="retrieving-a-value-from-a-property"></a>Извлечение значения из свойства
Вот как получить значение свойства в C++/CX.

```cppcx
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

Обратите внимание, что **PresenceDeviceRecords** возвращает объект среды выполнения Windows, у которого, в свою очередь, есть функция **Size**. Так как возвращенный объект также является проецируемым типом C++/WinRT, необходимо выполнить разыменование, используя оператор-точку для вызова **Size** .

### <a name="setting-a-property-to-a-new-value"></a>Задание свойству нового значения
Задание свойству нового значения выполняется по аналогичной схеме. Во-первых, это делается в C++/CX.

```cppcx
record->UserState = newValue;
```

Чтобы реализовать эквивалент в C++/WinRT, вызовите функцию с тем же именем, что и у свойства, а затем передайте аргумент.

```cppwinrt
record.UserState(newValue);
```

## <a name="creating-an-instance-of-a-class"></a>Создание экземпляра класса
Работать с C++/CX объект через дескриптор, который обычно называется hat (\^) ссылку. Создание нового объекта выполняется с помощью ключевого слова `ref new`, которое, в свою очередь, вызывает [**RoActivateInstance**](https://docs.microsoft.com/windows/desktop/api/roapi/nf-roapi-roactivateinstance), чтобы активировать новый экземпляр класса среды выполнения.

```cppcx
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

```cppcx
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

## <a name="converting-from-a-base-runtime-class-to-a-derived-one"></a>Преобразование из среды выполнения базового класса в производном один
Это часто используются ссылки на базовый, вы знаете, ссылается на объект производного типа. В C++/CX, использовать `dynamic_cast` для *приведения* ссылку на базовый в ссылку на производный. `dynamic_cast` Является просто скрытый вызов [ **QueryInterface**](https://docs.microsoft.com/windows/desktop/api/unknwn/nf-unknwn-iunknown-queryinterface(q_)). Ниже приведен типичный пример&mdash;при обработке события изменения свойства зависимостей, и вы хотите приведении **DependencyObject** в фактический тип, которому принадлежит свойство зависимостей.

```cppcx
void BgLabelControl::OnLabelChanged(Windows::UI::Xaml::DependencyObject^ d, Windows::UI::Xaml::DependencyPropertyChangedEventArgs^ e)
{
    BgLabelControl^ theControl{ dynamic_cast<BgLabelControl^>(d) };

    if (theControl != nullptr)
    {
        // succeeded ...
    }
}
```

Эквивалент C++/WinRT код заменяет `dynamic_cast` вызовом [ **IUnknown::try_as** ](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntry_as-function) функцию, которая инкапсулирует **QueryInterface**. Вы также можете вызвать [ **IUnknown::as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function), вместо этого, который создает исключение, если не возвращается, запрашивая требуемый интерфейс (интерфейс по умолчанию для типа, запрашивается). Вот C++/WinRT пример кода.

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

```cppcx
auto token = myButton->Click += ref new RoutedEventHandler([=](Platform::Object^ sender, RoutedEventArgs^ args)
{
    // Handle the event.
    // Note: locals are captured by value, not reference, since this handler is delayed.
});
```

Это эквивалентно и для C++/WinRT.

```cppwinrt
auto token = myButton().Click([=](IInspectable const& sender, RoutedEventArgs const& args)
{
    // Handle the event.
    // Note: locals are captured by value, not reference, since this handler is delayed.
});
```

Вместо лямбда-функции вы можете реализовать делегат в качестве свободной функции или в качестве указателя на функцию-член. Дополнительные сведения см. в разделе [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md).

При переносе из кода на C++/CX, где события и делегаты используются для внутренних целей (а не в разных двоичных файлах) [**winrt::delegate**](/uwp/cpp-ref-for-winrt/delegate) поможет вам выполнить репликацию этой модели в C++/WinRT. Также см. в разделе [параметризованные делегаты, простой сигналов и обратные вызовы в проекте](author-events.md#parameterized-delegates-simple-signals-and-callbacks-within-a-project).

## <a name="revoking-a-delegate"></a>Отзыв делегата
В C++/CX можно использовать оператор `-=`, чтобы отозвать регистрацию предыдущих событий.

```cppcx
myButton->Click -= token;
```

Это эквивалентно и для C++/WinRT.

```cppwinrt
myButton().Click(token);
```

Дополнительные сведения и описание параметров см. в разделе [Отзыв зарегистрированного делегата](handle-events.md#revoke-a-registered-delegate).

## <a name="mapping-ccx-platform-types-to-cwinrt-types"></a>Сопоставление типов **Platform** в C++/CX с типами C++/WinRT
C++/CX предоставляет несколько типов данных в пространстве имен **Platform**. Эти типы не являются типичными для C++, поэтому их можно использовать только при включении расширений языка среды выполнения Windows (Свойство проекта Visual Studio **C/C++**  > **Общие** > **Использовать расширение среды выполнения Windows** > **Да (/ZW)** ). В таблице ниже приведены инструкции по переносу из типов пространства имен **Platform** в их эквиваленты в C++/WinRT. После выполнения переноса, так как C++/WinRT является стандартной версией C++, параметр `/ZW` можно отключить.

| C++/CX | C++/WinRT |
| ---- | ---- |
| **Platform::Agile\^** | [**winrt::agile_ref**](/uwp/cpp-ref-for-winrt/agile-ref) |
| **Platform::Array\^** | См. в разделе [порт **Platform::Array\^** ](#port-platformarray) |
| **Platform::Exception\^** | [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error) |
| **Platform::InvalidArgumentException\^** | [**winrt::hresult_invalid_argument**](/uwp/cpp-ref-for-winrt/error-handling/hresult-invalid-argument) |
| **Platform::Object\^** | **WinRT::Windows::Foundation::IInspectable** |
| **Platform::String\^** | [**WinRT::hstring**](/uwp/cpp-ref-for-winrt/hstring) |

### <a name="port-platformagile-to-winrtagileref"></a>Порт **Platform::Agile\^**  для **winrt::agile_ref**
**Platform::Agile\^**  введите C++/CX представляет класс среды выполнения Windows, который может осуществляться из любого потока. C++/WinRT эквивалентом является [ **winrt::agile_ref**](/uwp/cpp-ref-for-winrt/agile-ref).

В C++/CX.

```cppcx
Platform::Agile<Windows::UI::Core::CoreWindow> m_window;
```

В C++/WinRT.

```cppwinrt
winrt::agile_ref<Windows::UI::Core::CoreWindow> m_window;
```

### <a name="port-platformarray"></a>Порт **Platform::Array\^**
Варианты использования списка инициализатора, **std::array**, или **std::vector**. Дополнительные сведения и примеры кода см. в разделе [списков инициализаторов стандартный](/windows/uwp/cpp-and-winrt-apis/std-cpp-data-types#standard-initializer-lists) и [стандартные массивы и векторы](/windows/uwp/cpp-and-winrt-apis/std-cpp-data-types#standard-arrays-and-vectors).

### <a name="port-platformexception-to-winrthresulterror"></a>Порт **Platform::Exception\^**  для **winrt::hresult_error**
**Platform::Exception\^**  типа создается в C++/CX возвращении API среды выполнения Windows не S\_HRESULT ОК. Для C++/WinRT эквивалентом является [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error).

К порту C++/WinRT, изменить любой код, использующий **Platform::Exception\^**  использовать **winrt::hresult_error**.

В C++/CX.

```cppcx
catch (Platform::Exception^ ex)
```

В C++/WinRT.

```cppwinrt
catch (winrt::hresult_error const& ex)
```

C++/WinRT предоставляет эти классы исключений.

| Тип исключения | Базовый класс | HRESULT |
| ---- | ---- | ---- |
| [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error) | | Вызовите метод [**hresult_error::to_abi**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error#hresult_errorto_abi-function) |
| [**winrt::hresult_access_denied**](/uwp/cpp-ref-for-winrt/error-handling/hresult-access-denied) | **winrt::hresult_error** | E_ACCESSDENIED |
| [**WinRT::hresult_canceled**](/uwp/cpp-ref-for-winrt/error-handling/hresult-canceled) | **winrt::hresult_error** | ERROR_CANCELLED |
| [**winrt::hresult_changed_state**](/uwp/cpp-ref-for-winrt/error-handling/hresult-changed-state) | **winrt::hresult_error** | E_CHANGED_STATE |
| [**WinRT::hresult_class_not_available**](/uwp/cpp-ref-for-winrt/error-handling/hresult-class-not-available) | **winrt::hresult_error** | CLASS_E_CLASSNOTAVAILABLE |
| [**winrt::hresult_illegal_delegate_assignment**](/uwp/cpp-ref-for-winrt/error-handling/hresult-illegal-delegate-assignment) | **winrt::hresult_error** | E_ILLEGAL_DELEGATE_ASSIGNMENT |
| [**WinRT::hresult_illegal_method_call**](/uwp/cpp-ref-for-winrt/error-handling/hresult-illegal-method-call) | **winrt::hresult_error** | E_ILLEGAL_METHOD_CALL |
| [**winrt::hresult_illegal_state_change**](/uwp/cpp-ref-for-winrt/error-handling/hresult-illegal-state-change) | **winrt::hresult_error** | E_ILLEGAL_STATE_CHANGE |
| [**winrt::hresult_invalid_argument**](/uwp/cpp-ref-for-winrt/error-handling/hresult-invalid-argument) | **winrt::hresult_error** | E_INVALIDARG |
| [**winrt::hresult_no_interface**](/uwp/cpp-ref-for-winrt/error-handling/hresult-no-interface) | **winrt::hresult_error** | E_NOINTERFACE |
| [**winrt::hresult_not_implemented**](/uwp/cpp-ref-for-winrt/error-handling/hresult-not-implemented) | **winrt::hresult_error** | E_NOTIMPL |
| [**WinRT::hresult_out_of_bounds**](/uwp/cpp-ref-for-winrt/error-handling/hresult-out-of-bounds) | **winrt::hresult_error** | E_BOUNDS |
| [**winrt::hresult_wrong_thread**](/uwp/cpp-ref-for-winrt/error-handling/hresult-wrong-thread) | **winrt::hresult_error** | RPC_E_WRONG_THREAD |

Обратите внимание, что каждый класс (через базовый класс **hresult_error**) предоставляет функцию [**to_abi**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error#hresult_errorto_abi-function), которая возвращает HRESULT ошибки, и функцию [**message**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error#hresult_errormessage-function), которая возвращает строковое представление HRESULT.

Ниже приведен пример создания исключения в C++/CX.

```cppcx
throw ref new Platform::InvalidArgumentException(L"A valid User is required");
```

А также эквивалент в C++/WinRT.

```cppwinrt
throw winrt::hresult_invalid_argument{ L"A valid User is required" };
```

### <a name="port-platformobject-to-winrtwindowsfoundationiinspectable"></a>Порт **Platform::Object\^**  для **winrt::Windows::Foundation::IInspectable**
Как и все типы C++/WinRT, **winrt::Windows::Foundation::IInspectable** —это тип значения. Ниже описано, как инициализировать переменную этого типа в значение null.

```cppwinrt
winrt::Windows::Foundation::IInspectable var{ nullptr };
```

### <a name="port-platformstring-to-winrthstring"></a>Порт **Platform::String\^**  для **winrt::hstring**
**Platform::String\^**  эквивалентно типу ABI HSTRING среды выполнения Windows. Для C++/WinRT эквивалентом является [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring). Однако с помощью C++/WinRT можно вызывать API среды выполнения Windows, используя типы широких строк стандартной библиотеки C++, такие как **std::wstring**, и/или литералы широких строк. Дополнительные сведения и примеры кода см. в разделе [Обработка строк в C++/WinRT](strings.md).

С помощью C++/CX, вы можете получить доступ к [ **Platform::String::Data** ](https://docs.microsoft.com/en-us/cpp/cppcx/platform-string-class#data) свойство для извлечения строки в стиле языка C **const wchar_t\***  (например, массив Чтобы передать его в **std::wcout**).

```cppcx
auto var{ titleRecord->TitleName->Data() };
```

Чтобы сделать то же самое в C++/WinRT, можно использовать функцию [**hstring::c_str**](/uwp/api/windows.foundation.uri.-ctor#Windows_Foundation_Uri__ctor_System_String_), чтобы получить версию строки, заканчивающуюся символом NULL (языка группы С), так же как и из **std::wstring**.

```cppwinrt
auto var{ titleRecord.TitleName().c_str() };
```

Когда дело доходит до реализации интерфейсов API, который принимает или возвращает строки, как правило, изменяется любой C++/CX кода, использующего **Platform::String\^**  использовать **winrt::hstring** вместо этого.

Вот пример API-интерфейса C++/CX, который принимает строковое значение.

```cppcx
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

#### <a name="tostring"></a>ToString()

C++Предоставляет /CX [Object::ToString](/cpp/cppcx/platform-object-class?view=vs-2017#tostring) метод.

```cppcx
int i{ 2 };
auto s{ i.ToString() }; // s is a Platform::String^ with value L"2".
```

C++/ WinRT не предоставляет эту функцию напрямую, но можно отключить для получения дополнительных сведений.

```cppwinrt
int i{ 2 };
auto s{ std::to_wstring(i) }; // s is a std::wstring with value L"2".
```

## <a name="important-apis"></a>Важные API
* [Структура шаблона WinRT::Delegate](/uwp/cpp-ref-for-winrt/delegate)
* [Структура WinRT::hresult_error](/uwp/cpp-ref-for-winrt/error-handling/hresult-error)
* [Структура WinRT::hstring](/uwp/cpp-ref-for-winrt/hstring)
* [Пространство имен WinRT](/uwp/cpp-ref-for-winrt/winrt)

## <a name="related-topics"></a>См. также
* [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx)
* [Создавать события в C++/WinRT](author-events.md)
* [Параллелизм и асинхронные операции с C++/WinRT](concurrency.md)
* [Использование интерфейсов API с помощью C++/WinRT](consume-apis.md)
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)
* [Взаимодействие между C++/WinRT и C++/CX](interop-winrt-cx.md)
* [Справочник по Microsoft интерфейс определения языка 3.0](/uwp/midl-3)
* [Переход на C++/WinRT с WRL](move-to-winrt-from-wrl.md)
* [Строка, обработка в C++/WinRT](strings.md)
