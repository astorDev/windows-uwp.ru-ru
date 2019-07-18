---
description: В этом разделе объясняется, как перенести код C++/CX в его эквивалент на C++/WinRT.
title: Переход на C++/WinRT из C++/CX
ms.date: 01/17/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, port, migrate, C++/CX
ms.localizationpriority: medium
ms.openlocfilehash: d2b92bf5e265c2d596a7fc7eb54b127010cee897
ms.sourcegitcommit: a7a1e27b04f0ac51c4622318170af870571069f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717608"
---
# <a name="move-to-cwinrt-from-ccx"></a>Переход на C++/WinRT из C++/CX

В этом разделе показано, как перенести код в [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx) проекте на его эквивалент в [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt).

## <a name="porting-strategies"></a>Стратегии переноса

Если вам нужно постепенно переносить код C++/CX на C++/WinRT, то вы сможете это сделать. Коды C++/CX и C++/WinRT могут сосуществовать в одном проекте, за исключением поддержки компилятора XAML и компонентов среды выполнения Windows. Для этих двух исключений необходимо выбрать либо C++/CX, либо C++/WinRT в одном и том же проекте.

> [!IMPORTANT]
> Если проект создает приложение XAML, рекомендуем сначала создать новый проект в Visual Studio с помощью одного из шаблонов проектов C++/WinRT (см. раздел [Поддержка Visual Studio для C++/WinRT](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package)). Затем начните копировать исходный код и разметку из проекта C++/CX. Вы можете добавить новые страницы XAML с помощью команды **Проект** \> **Добавить новый элемент...** \> **Visual C++**   >  **Пустая страница (C++/WinRT)** .
>
> Кроме того, можно использовать компонент среды выполнения Windows для идентификации кода из проекта XAML C++/CX во время его портирования. Либо перенесите в компонент как можно больше кода C++/CX, а затем измените проект XAML на C++/WinRT. Или же оставьте проект XAML в C++/CX, создайте новый компонент C++/WinRT и начните переносить код C++/CX из проекта XAML в новый компонент. Также имея проект компонента C++/CX вместе с проектом компонента C++/WinRT в рамках одного решения, можно ссылаться на них из проекта приложения и постепенно переносить из одного в другой. Дополнительные сведения об использовании двух языковых проекций в рамках одного проекта см. в статье [Взаимодействия между C++/WinRT и C++/CX](interop-winrt-cx.md).

> [!NOTE]
> [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx) и Windows SDK объявляют типы в корневом пространстве имен **Windows**. Тип Windows, спроецированный в C++/WinRT, имеет такое же полное имя, что и тип Windows, но он помещен в пространство имен C++ **winrt**. Эти различные пространства имен позволяют выполнять перенос из C++/CX в C++/WinRT в удобном для вас темпе.

С учетом вышеупомянутых исключений, первым шагом при переносе проекта C++/CX на C++/WinRT является ручное добавление в него поддержки C++/WinRT (см. статью [Поддержка Visual Studio для C++/WinRT](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package)). Для этого в свой проект следует установить [пакет Microsoft.Windows.CppWinRT NuGet](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/). В Visual Studio откройте проект, щелкните **Проект** \> **Управление пакетами NuGet...** \> **Обзор**, введите или вставьте **Microsoft.Windows.CppWinRT** в поле поиска, выберите элемент в результатах поиска, а затем нажмите кнопку **Установить**, чтобы установить пакет для этого проекта. Одним из последствий этого изменения является отключение поддержки для C++/CX в проекте. Рекомендуется оставить поддержку отключенной, чтобы сообщения сборки помогли вам найти (и перенести) все зависимости на C++ CX. Либо можно снова включить поддержку (в свойствах проекта **C/C++** \> **Общие** \> **Использование расширения среды выполнения Windows** \> **Да (/ZW)** ), и выполнить постепенный перенос.

Убедитесь, что для свойства проекта **Общие** \> **Версия целевой платформы** установлено значение 10.0.17134.0 (Windows 10, версия 1803) или выше.

В предварительно скомпилированный файл заголовка (обычно это `pch.h`) добавьте `winrt/base.h`.

```cppwinrt
#include <winrt/base.h>
```

При добавлении каких-либо спроектированных заголовков Windows API для C++/WinRT, (например, `winrt/Windows.Foundation.h`) необходимость в явном добавлении `winrt/base.h` подобным образом отсутствует, так как этот элемент будет добавлен автоматически.

Если в проекте также используются [типы библиотек шаблонов C++ (WRL) среды выполнения Windows](/cpp/windows/windows-runtime-cpp-template-library-wrl), см. раздел [Переход на C++/WinRT из WRL](move-to-winrt-from-wrl.md).

## <a name="parameter-passing"></a>Передача параметров
При написании исходного кода C++/CX вы передаете типы C++/CX в качестве параметров функции в виде ссылок на шляпку(\^).

```cppcx
void LogPresenceRecord(PresenceRecord^ record);
```

В C++/WinRT для синхронных функций следует использовать параметры `const&` по умолчанию. Это позволит избежать создания копий и блокировки служебных данных. Однако сопрограммы должны использовать передачу параметров по значению, чтобы гарантировать их захват по значению и избежать проблем со временем жизни (дополнительные сведения см. в разделе [Параллельная обработка и асинхронные операции с помощью C++/WinRT](concurrency.md)).

```cppwinrt
void LogPresenceRecord(PresenceRecord const& record);
IASyncAction LogPresenceRecordAsync(PresenceRecord const record);
```

Объект С++/WinRT — это, по сути, значение, которое содержит указатель интерфейса на вспомогательный объект среды выполнения Windows. При копировании объекта C++/WinRT компилятор копирует инкапсулированный указатель интерфейса, увеличивая количество его ссылок. Окончательное уничтожение копии подразумевает уменьшение количества его ссылок. Таким образом, при необходимости теряются только служебные данные копии .

## <a name="variable-and-field-references"></a>Ссылки переменных и полей
При написании исходного кода C++/CX вы используете переменные с шляпки (\^) для ссылки на объекты среды выполнения Windows и оператора стрелки (- &gt;) для разыменования переменной с шляпки.

```cppcx
IVectorView<User^>^ userList = User::Users;

if (userList != nullptr)
{
    for (UINT32 iUser = 0; iUser < userList->Size; ++iUser)
    ...
```

При переносе на эквивалентный код C++/WinRT, можно пойти длинным путем, удаляя все шляпки и заменяя оператор стрелки (-&gt;) на оператор точки (.). Проектируемые типы C++ WinRT являются значениями, а не указателями.

```cppwinrt
IVectorView<User> userList = User::Users();

if (userList != nullptr)
{
    for (UINT32 iUser = 0; iUser < userList.Size(); ++iUser)
    ...
```

Конструктор по умолчанию для шляпки-указателя C++/CX инициализирует его как ноль. Вот пример кода C++/CX, в котором мы создаем переменную или поле правильного типа, но не инициализированные. Другими словами, изначально он не ссылается на **TextBlock**; мы намерены назначить ссылку позже.

```cppcx
TextBlock^ textBlock;

class MyClass
{
    TextBlock^ textBlock;
};
```

Об эквиваленте в C++/WinRT см. в статье [Отложенная инициализация](consume-apis.md#delayed-initialization).

## <a name="properties"></a>Свойства
Расширения языка C++/CX включают концепцию свойств. При написании исходного кода C++/CX вы можете обращаться к свойству, как если бы оно было полем. В стандартной версии C++ отсутствует концепция свойств, поэтому в C++/WinRT осуществляется вызов функций get и set.

В следующих примерах **XboxUserId**, **UserState**, **PresenceDeviceRecords** и **Size** являются свойствами.

### <a name="retrieving-a-value-from-a-property"></a>Извлечение значения из свойства
Получить значение свойства в C++/CX можно следующим образом.

```cppcx
void Sample::LogPresenceRecord(PresenceRecord^ record)
{
    auto id = record->XboxUserId;
    auto state = record->UserState;
    auto size = record->PresenceDeviceRecords->Size;
}
```

Эквивалентный исходный код C++/WinRT вызывает функцию с тем же именем, что и у свойства, но без параметров.

```cppwinrt
void Sample::LogPresenceRecord(PresenceRecord const& record)
{
    auto id = record.XboxUserId();
    auto state = record.UserState();
    auto size = record.PresenceDeviceRecords().Size();
}
```

Обратите внимание, что функция **PresenceDeviceRecords** возвращает объект среды выполнения Windows, который сам по себе имеет функцию **Size**. Поскольку возвращаемый объект также является проецируемым типом C++/WinRT, его разыменовываем с помощью оператора точки для вызова **Size**.

### <a name="setting-a-property-to-a-new-value"></a>Задание свойству нового значения
Задание свойству нового значения выполняется по аналогичной схеме. Во-первых, это делается в C++/CX.

```cppcx
record->UserState = newValue;
```

Чтобы сделать аналог в C++/WinRT, вызовете функцию с тем же именем, что и у свойства, и передайте аргумент.

```cppwinrt
record.UserState(newValue);
```

## <a name="creating-an-instance-of-a-class"></a>Создание экземпляра класса
Работа с объектом C++/CX через дескриптор, обычно именуемый ссылкой шляпкой (\^). Создайте новый объект с помощью ключевого слова `ref new`, которое, в свою очередь, вызывает [ **RoActivateInstance** ](https://docs.microsoft.com/windows/desktop/api/roapi/nf-roapi-roactivateinstance) для активации нового экземпляра класса среды выполнения.

```cppcx
using namespace Windows::Storage::Streams;

class Sample
{
private:
    Buffer^ m_gamerPicBuffer = ref new Buffer(MAX_IMAGE_SIZE);
};
```

Объект C++/WinRT является значением; поэтому его можно разместить в стеке или как поле объекта. Для размещения объекта C++/WinRT *никогда* не следует использовать `ref new` (или `new`). **RoActivateInstance** продолжает вызываться в фоновом режиме.

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

Перенос одинакового кода в C++/WinRT. Обратите внимание на использование конструктора **std::nullptr_t**. Дополнительные сведения об этом конструкторе см. в разделе [Использование API с помощью C++/WinRT](consume-apis.md#delayed-initialization).

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

## <a name="converting-from-a-base-runtime-class-to-a-derived-one"></a>Преобразование из базового класса времени выполнения в производный
Как правило, ссылка на базу, как вам известно, указывает на объект производного типа. В C++/CX используйте `dynamic_cast` для *преобразования* ссылки на базу в ссылку на производную. `dynamic_cast` — это просто скрытый вызов [**QueryInterface**](https://docs.microsoft.com/windows/desktop/api/unknwn/nf-unknwn-iunknown-queryinterface(q_)). Ниже приведен типичный пример &mdash; при обработке события изменения свойства зависимостей, нужно привести из **DependencyObject** обратно к фактическому типу, которому принадлежит свойство зависимости.

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

Эквивалентный код C++/WinRT заменяет `dynamic_cast` вызовом функции [**IUnknown::try_as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntry_as-function), которая инкапсулирует **QueryInterface**. Также есть возможность вместо этого вызвать [**IUnknown::as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function), который создает исключение, если запрос для требуемого интерфейса (интерфейс по умолчанию запрашиваемого вами типа) не возвращен. Ниже приведен пример кода C++/WinRT.

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
Вот типичный пример обработки события в C++/CX с использованием лямбда-функции в качестве делегата в этом случае.

```cppcx
auto token = myButton->Click += ref new RoutedEventHandler([=](Platform::Object^ sender, RoutedEventArgs^ args)
{
    // Handle the event.
    // Note: locals are captured by value, not reference, since this handler is delayed.
});
```

Это эквивалент в C++/WinRT.

```cppwinrt
auto token = myButton().Click([=](IInspectable const& sender, RoutedEventArgs const& args)
{
    // Handle the event.
    // Note: locals are captured by value, not reference, since this handler is delayed.
});
```

Вместо лямбда-функции вы можете использовать свой делегат как свободную функцию или как указатель на член-функцию. Дополнительные сведения см. в разделе [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md).

При переносе из базы кода C++/CX, где события и делегаты используются для внутренних целей (а не через двоичные файлы) [**winrt::delegate**](/uwp/cpp-ref-for-winrt/delegate) поможет скопировать этот шаблон в C++/WinRT. Также см. статью [Параметризованные делегаты, простые сигналы и обратные вызовы в проекте](author-events.md#parameterized-delegates-simple-signals-and-callbacks-within-a-project).

## <a name="revoking-a-delegate"></a>Отзыв делегата
В C++/CX можно использовать оператор `-=`, чтобы отозвать регистрацию предыдущих событий.

```cppcx
myButton->Click -= token;
```

Это эквивалент в C++/WinRT.

```cppwinrt
myButton().Click(token);
```

Дополнительные сведения и описание параметров см. в разделе [Отзыв зарегистрированного делегата](handle-events.md#revoke-a-registered-delegate).

## <a name="mapping-ccx-platform-types-to-cwinrt-types"></a>Сопоставление типов **Platform** в C++/CX с типами C++/WinRT
C++/CX предоставляет несколько типов данных в пространстве имен **Platform**. Эти типы не являются стандартными для C++, поэтому их можно использовать только при включении языковых расширений среды выполнения Windows (свойство проекта Visual Studio **C/C++**  > **Общие** > **Использовать расширение среды выполнения Windows** > **Да (/ZW)** ). В таблице ниже приведены инструкции по переносу из типов пространства имен **Platform** в их эквиваленты в C++/WinRT. После выполнения переноса, так как C++/WinRT является стандартной версией C++, параметр `/ZW` можно отключить.

| C++/CX | C++/WinRT |
| ---- | ---- |
| **Platform::Agile\^** | [**winrt::agile_ref**](/uwp/cpp-ref-for-winrt/agile-ref) |
| **Platform::Array\^** | См. [Перенос **Platform::Array\^** ](#port-platformarray) |
| **Platform::Exception\^** | [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error) |
| **Platform::InvalidArgumentException\^** | [**winrt::hresult_invalid_argument**](/uwp/cpp-ref-for-winrt/error-handling/hresult-invalid-argument) |
| **Platform::Object\^** | **winrt::Windows::Foundation::IInspectable** |
| **Platform::String\^** | [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring) |

### <a name="port-platformagile-to-winrtagileref"></a>Перенос **Platform::Agile\^** в **winrt::agile_ref**
Тип **Platform::Agile\^** в C ++ / CX представляет класс среды выполнения Windows, к которому можно получить доступ из любого потока. Для C++/WinRT эквивалентом является [**winrt::agile_ref**](/uwp/cpp-ref-for-winrt/agile-ref).

В C++/CX.

```cppcx
Platform::Agile<Windows::UI::Core::CoreWindow> m_window;
```

В C++/WinRT.

```cppwinrt
winrt::agile_ref<Windows::UI::Core::CoreWindow> m_window;
```

### <a name="port-platformarray"></a>Перенос **Platform::Array\^**
Для вас доступны использование списка инициализатора, **std::array** или **std::vector**. Дополнительные сведения и примеры кода см. в статьях [Стандартные списки инициализаторов](/windows/uwp/cpp-and-winrt-apis/std-cpp-data-types#standard-initializer-lists) и [Стандартные массивы и векторы](/windows/uwp/cpp-and-winrt-apis/std-cpp-data-types#standard-arrays-and-vectors).

### <a name="port-platformexception-to-winrthresulterror"></a>Перенесите **Platform::Exception\^\^** в **winrt::hresult_error**
Тип **Platform::Exception\^** создается в C++/CX, когда API среда выполнения Windows возвращается к не S\_OK HRESULT. Для C++/WinRT эквивалентом является [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error).

Чтобы выполнить перенос в C++/WinRT, внесите изменения в код так, чтобы вместо **Platform::Exception\^** использовалось **winrt::hresult_error**.

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
| [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error) | | Вызовите [**hresult_error::to_abi**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error#hresult_errorto_abi-function) |
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

Обратите внимание, что каждый класс (через базовый класс **hresult_error**) предоставляет функцию [**to_abi**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error#hresult_errorto_abi-function), которая возвращает HRESULT ошибки, и функцию [**message**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error#hresult_errormessage-function), которая возвращает строковое представление HRESULT.

Ниже приведен пример создания исключения в C++/CX.

```cppcx
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

С помощью C++/CX можно получить доступ к свойству [**Platform::String::Data**](https://docs.microsoft.com/cpp/cppcx/platform-string-class?view=vs-2019#data), чтобы получить строку в виде массива **const wchar_t\*** языков группы C (например, чтобы передать ее в **std::wcout**).

```cppcx
auto var{ titleRecord->TitleName->Data() };
```

Чтобы сделать то же самое в C++/WinRT, можно использовать функцию [**hstring::c_str**](/uwp/api/windows.foundation.uri.-ctor#Windows_Foundation_Uri__ctor_System_String_), чтобы получить версию строки, заканчивающуюся символом NULL на языке группы С, так же как и из **std::wstring**.

```cppwinrt
auto var{ titleRecord.TitleName().c_str() };
```

При необходимости реализации API-интерфейсов, получающих или возвращающих строки, как правило, требуется изменить любой код C++/CX, использующий **Platform::String\^** , чтобы вместо него применить **winrt::hstring**.

Вот пример API-интерфейса C++/CX, принимающего строковое значение.

```cppcx
void LogWrapLine(Platform::String^ str);
```

Для C++/WinRT этот API-интерфейс можно объявить таким же образом в [MIDL 3.0](/uwp/midl-3).

```idl
// LogType.idl
void LogWrapLine(String str);
```

Затем цепочка инструментов C++/WinRT сгенерирует исходный код, который выглядит следующим образом.

```cppwinrt
void LogWrapLine(winrt::hstring const& str);
```

#### <a name="tostring"></a>ToString()

C++/CX предоставляет метод [Object::ToString](/cpp/cppcx/platform-object-class?view=vs-2017#tostring).

```cppcx
int i{ 2 };
auto s{ i.ToString() }; // s is a Platform::String^ with value L"2".
```

C++/ WinRT не предоставляет эту функцию напрямую, но можно обратиться к ее альтернативам.

```cppwinrt
int i{ 2 };
auto s{ std::to_wstring(i) }; // s is a std::wstring with value L"2".
```

## <a name="important-apis"></a>Важные API
* [winrt::delegate struct template](/uwp/cpp-ref-for-winrt/delegate)
* [winrt::hresult_error struct](/uwp/cpp-ref-for-winrt/error-handling/hresult-error)
* [Структура winrt::hstring](/uwp/cpp-ref-for-winrt/hstring)
* [Пространство имен WinRT](/uwp/cpp-ref-for-winrt/winrt)

## <a name="related-topics"></a>Статьи по теме
* [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx)
* [Создание событий в C++/WinRT](author-events.md)
* [Параллельные обработка и выполнение асинхронных операций с помощью C++/WinRT](concurrency.md)
* [Использование интерфейсов API с помощью C++/WinRT](consume-apis.md)
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)
* [Взаимодействие между C++/WinRT и C++/CX](interop-winrt-cx.md)
* [Справочник по языку Microsoft Interface Definition Language 3.0](/uwp/midl-3)
* [Переход на C++/WinRT с WRL](move-to-winrt-from-wrl.md)
* [Обработка строк в C++/WinRT](strings.md)
