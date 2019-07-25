---
description: В этом разделе обсуждаются стратегии обработки ошибок при программировании на C++/WinRT.
title: Обработка ошибок в C++/WinRT
ms.date: 04/23/2019
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, ошибка, обработка, исключение
ms.localizationpriority: medium
ms.openlocfilehash: 37819d1626d3adc6f5647f447567a9273e72668d
ms.sourcegitcommit: d37a543cfd7b449116320ccfee46a95ece4c1887
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68270132"
---
# <a name="error-handling-with-cwinrt"></a>Обработка ошибок в C++/WinRT

В этом разделе обсуждаются стратегии обработки ошибок при программировании на [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt). Общие и дополнительные сведения см. в статье [Обработка ошибок и исключений (современный C++)](/cpp/cpp/errors-and-exception-handling-modern-cpp).

## <a name="avoid-catching-and-throwing-exceptions"></a>Избегайте создания и перехвата исключений
Мы рекомендуем по-прежнему писать [код с обработкой исключений](/cpp/cpp/how-to-design-for-exception-safety), но при этом избегать создания и перехвата исключений, когда это возможно. Если отсутствует обработчик исключения, Windows автоматически создает отчет об ошибках (в том числе минидамп сбоя), который поможет вам отследить проблему.

Не создавайте исключение, которое вы собираетесь перехватить. И не используйте исключения для ожидаемых ошибок. Создавайте исключение *только при возникновении непредвиденной ошибки при исполнении* и обрабатывайте все остальные проблемы с помощью кодов ошибок и результатов напрямую, рядом с источником сбоя. Таким образом, при *вызове* исключения вы будете знать, что его причина заключается в ошибке в вашем коде или в состоянии исключения системы.

Рассмотрим сценарий доступа к реестру Windows. Если вашему приложению не удается прочитать значение из реестра, следует ожидать ошибку, которую необходимо корректно обработать. Не вызывайте исключение, а верните значение `bool` или `enum`, указывающее, что значение не было прочитано и, возможно, почему это произошло. Тогда как ошибка *записи* значения в реестр скорее всего указывает на более серьезную проблему, которую невозможно обработать в вашем приложении. В таком случае не следует продолжать работу приложения, поэтому создание исключения, которое создает отчет об ошибках, — этой самый быстрый способ предотвращения дальнейших проблем.

В качестве другого примера рассмотрим ситуацию с получением эскиза изображения из вызова метода [**StorageFile.GetThumbnailAsync**](/uwp/api/windows.storage.storagefile.getthumbnailasync#Windows_Storage_StorageFile_GetThumbnailAsync_Windows_Storage_FileProperties_ThumbnailMode_) и последующей передачей этого эскиза в [**BitmapSource.SetSourceAsync**](/uwp/api/windows.ui.xaml.media.imaging.bitmapsource.setsourceasync#Windows_UI_Xaml_Media_Imaging_BitmapSource_SetSourceAsync_Windows_Storage_Streams_IRandomAccessStream_). Если в результате такой последовательности вызовов `nullptr` передается в **SetSourceAsync** (не удалось прочитать файл изображения; возможно, файл не содержит изображение, хотя расширение указывает на обратное), это приведет к вызову исключения из-за недопустимого указателя. Если вы обнаружили подобное в своем коде, вместо перехвата и обработки исключения проверьте значение `nullptr`, возвращенное **GetThumbnailAsync**.

Создание исключений обычно занимает больше времени, чем использование кодов ошибок. Если вы вызываете исключение только при возникновении неустранимой ошибки, производительность программы будет оптимальной при ее нормальной работе.

Но ее производительность снизится из-за необходимости вызова соответствующих деструкторов при вызове исключения. Издержки такого решения зависят от того, вызывается исключение или нет. Поэтому вам необходимо убедиться, что компилятор дает хорошее представление о том, какие функции могут вызвать исключения. Если компилятор убедит вас в том, что определенные функции не будут вызывать исключений (спецификация `noexcept`), значит он может оптимизировать создаваемый код.

## <a name="catching-exceptions"></a>Перехват исключений
Ошибка, которая возникает на уровне [ABI среды выполнения Windows](interop-winrt-abi.md#what-is-the-windows-runtime-abi-and-what-are-abi-types), возвращается в виде значения HRESULT. Но вам не нужно обрабатывать HRESULT в коде. Код проекции C++/WinRT, который создается для API на стороне потребителя, обнаруживает код ошибки HRESULT на уровне ABI и преобразует код в исключение [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error), которое можно перехватить и обработать. Если вы *точно* хотите обработать значения HRESULT, тогда используйте тип **winrt::hresult**.

Например, если пользователь удалит изображение из библиотеки изображений, когда ваше приложение работает с этой коллекцией, проекция создаст исключение. И это тот случай, когда вам нужно перехватить и обработать такое исключение. Вот соответствующий пример кода.

```cppwinrt
#include <winrt/Windows.Foundation.Collections.h>
#include <winrt/Windows.Storage.h>
#include <winrt/Windows.UI.Xaml.Media.Imaging.h>

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Storage;
using namespace Windows::UI::Xaml::Media::Imaging;

IAsyncAction MakeThumbnailsAsync()
{
    auto imageFiles{ co_await KnownFolders::PicturesLibrary().GetFilesAsync() };

    for (StorageFile const& imageFile : imageFiles)
    {
        BitmapImage bitmapImage;
        try
        {
            auto thumbnail{ co_await imageFile.GetThumbnailAsync(FileProperties::ThumbnailMode::PicturesView) };
            if (thumbnail) bitmapImage.SetSource(thumbnail);
        }
        catch (winrt::hresult_error const& ex)
        {
            winrt::hresult hr = ex.to_abi(); // HRESULT_FROM_WIN32(ERROR_FILE_NOT_FOUND).
            winrt::hstring message = ex.message(); // The system cannot find the file specified.
        }
    }
}
```

Используйте этот шаблон в сопрограмме при вызове функции `co_await`. Еще одним примером такого преобразования HRESULT в исключение является ситуация, когда API компонента возвращает E_OUTOFMEMORY, что приводит к вызову исключения **std::bad_alloc**.

## <a name="throwing-exceptions"></a>Создание исключений
В некоторых ситуациях при сбое вызова определенной функции ваше приложение не сможет восстановить работу (вы больше не сможете рассчитывать на его предсказуемую работу). В примере кода ниже используется значение [**winrt::handle**](/uwp/cpp-ref-for-winrt/handle) в качестве оболочки для значения HANDLE, возвращенного от [**CreateEvent**](https://docs.microsoft.com/windows/desktop/api/synchapi/nf-synchapi-createeventa). Затем дескриптор передается шаблону функции [**winrt::check_bool**](/uwp/cpp-ref-for-winrt/error-handling/check-bool) (с созданием значения `bool`). Функция **winrt::check_bool** работает с `bool` и любыми значениями, которые можно преобразовать в `false` (состояние ошибки) или `true` (состояние успеха).

```cppwinrt
winrt::handle h{ ::CreateEvent(nullptr, false, false, nullptr) };
winrt::check_bool(bool{ h });
winrt::check_bool(::SetEvent(h.get()));
```

Если значение, которое вы передаете функции [**winrt::check_bool**](/uwp/cpp-ref-for-winrt/error-handling/check-bool), является false, выполняются следующие действия.

- **winrt::check_bool** вызывает функцию [**winrt::throw_last_error**](/uwp/cpp-ref-for-winrt/error-handling/throw-last-error).
- **winrt::throw_last_error** вызывает [**GetLastError**](https://docs.microsoft.com/windows/desktop/api/errhandlingapi/nf-errhandlingapi-getlasterror) для извлечения значения последнего кода ошибки вызывающего потока, а затем вызывает функцию [**winrt::throw_hresult**](/uwp/cpp-ref-for-winrt/error-handling/throw-hresult).
- **winrt::throw_hresult** вызывает исключение, используя объект [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error) (или стандартный объект), представляющий этот код ошибки.

Поскольку API Windows сообщают об ошибках во время выполнения с помощью различных типов возвращаемых значений, в дополнение к **winrt::check_bool** используется ряд других полезных вспомогательных функций для проверки значений и создания исключений.

- [**winrt::check_hresult**](/uwp/cpp-ref-for-winrt/error-handling/check-hresult). Проверяет, представляет ли код HRESULT ошибку, и, если это так, вызывает **winrt::throw_hresult**.
- [**winrt::check_nt**](/uwp/cpp-ref-for-winrt/error-handling/check-nt). Проверяет, представляет ли код ошибку, и, если это так, вызывает **winrt::throw_hresult**.
- [**winrt::check_pointer**](/uwp/cpp-ref-for-winrt/error-handling/check-pointer). Проверяет, имеет ли указатель значение NULL, и, если это так, вызывает **winrt::throw_last_error**.
- [**winrt::check_win32**](/uwp/cpp-ref-for-winrt/error-handling/check-win32). Проверяет, представляет ли код ошибку, и, если это так, вызывает **winrt::throw_hresult**.

Вы можете использовать эти вспомогательные функции для стандартных типов кода возврата или можете реагировать на любые ошибки и вызывать [**winrt::throw_last_error**](/uwp/cpp-ref-for-winrt/error-handling/throw-last-error) или [**winrt::throw_hresult**](/uwp/cpp-ref-for-winrt/error-handling/throw-hresult). 

## <a name="throwing-exceptions-when-authoring-an-api"></a>Вызов исключений при создании API
Все границы [ABI среды выполнения Windows](interop-winrt-abi.md#what-is-the-windows-runtime-abi-and-what-are-abi-types) должны быть помечены как *noexcept*. Это означает, что исключения не могут выходить за эти границы. При создании API следует всегда помечать границу ABI ключевым словом `noexcept` (C++). В C++ поведение `noexcept` специфично. Если исключение C++ достигает границы `noexcept`, процесс быстро завершается с помощью **std::terminate**. Это поведение является предпочтительным, так как необработанное исключение почти всегда подразумевает неизвестное состояние в процессе.

Так как исключение не должно выходить за границы ABI, на уровне ABI возвращается ошибка, которая возникает в реализации, в виде кода ошибки HRESULT. При создании API с использованием C++/WinRT автоматически создается код для преобразования всех исключений, которые вы *вызываете* в вашей реализации, в значение HRESULT. Функция [**winrt::to_hresult**](/uwp/cpp-ref-for-winrt/error-handling/to-hresult) используется в этом коде следующим образом.

```cppwinrt
HRESULT DoWork() noexcept
{
    try
    {
        // Shim through to your C++/WinRT implementation.
        return S_OK;
    }
    catch (...)
    {
        return winrt::to_hresult(); // Convert any exception to an HRESULT.
    }
}
```

[**winrt::to_hresult**](/uwp/cpp-ref-for-winrt/error-handling/to-hresult) обрабатывает исключения, производные от **std::exception**, [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error) и ее производных типов. В реализации лучше всего использовать **winrt::hresult_error** или производный тип, чтобы пользователи вашего API получали расширенные сведения об ошибке. Значение **std::exception** (которое сопоставляется с E_FAIL) поддерживается, если исключения возникают при использовании стандартной библиотеки шаблонов.

### <a name="debuggability-with-noexcept"></a>Возможность отладки при использовании noexcept
Как упоминалось выше, если в C++ исключение достигает границы `noexcept`, процесс быстро завершается с помощью **std::terminate**. Это неидеальный вариант для отладки, так как **std::terminate** часто частично или полностью не учитывает контекст возникающей ошибки или исключения, в особенности при использовании сопрограмм.

Поэтому в этом разделе рассматривается ситуация, когда метод ABI (который вы правильно пометили `noexcept`) использует `co_await` для вызова асинхронного кода проекции C++/WinRT. Рекомендуется заключить вызовы в код проекции C++/WinRT в **winrt::fire_and_forget**. Это позволяет правильно записать в подходящем месте необработанное исключение в виде исключения заполнения, что существенно увеличивает шансы на выполнение отладки.

```cppwinrt
HRESULT MyWinRTObject::MyABI_Method() noexcept
{
    winrt::com_ptr<Foo> foo{ get_a_foo() };

    [/*no captures*/](winrt::com_ptr<Foo> foo) -> winrt::fire_and_forget
    {
        co_await winrt::resume_background();

        foo->ABICall();

        AnotherMethodWithLotsOfProjectionCalls();
    }(foo);

    return S_OK;
}
```

**winrt::fire_and_forget** имеет встроенный вспомогательный метод `unhandled_exception`, который вызывает **winrt::terminate**. Последний, в свою очередь, вызывает **RoFailFastWithErrorContext**. Это гарантирует, что любой контекст (исключение заполнения, код ошибки, сообщение об ошибке, обратная трассировка стека и т. д.) сохраняется для отладки в режиме реального времени или для дампа после неустранимой ошибки. Для удобства можно выделить часть "выполнить и забыть" в отдельную функцию, возвращающую **winrt::fire_and_forget**, а затем выполняющую вызов.

### <a name="synchronous-code"></a>Синхронный код
В некоторых случаях метод ABI (который, опять же, правильно помечен `noexcept`) вызывает только синхронный код. Иными словами, он никогда не использует `co_await`, чтобы вызывать асинхронный метод среды выполнения Windows или переключаться между активными и фоновыми потоками. В этом случае методика "выполнить и забыть" будет работать, но это будет неэффективно. Вместо этого можно сделать примерно следующее.

```cppwinrt
HRESULT abi() noexcept try
{
    // ABI code goes here.
} catch (...) { winrt::terminate(); }
```

### <a name="fail-fast"></a>Быстрое завершение со сбоем
Выполнение кода из предыдущего раздела все так же быстро завершается сбоем. Как уже упоминалось, этот код не обрабатывает исключения. Любое необработанное исключение приводит к завершению программы.

Но такая форма предпочтительна, так как она обеспечивает возможность отладки. В редких случаях может потребоваться выполнить `try/catch` и обработать определенные исключения. Но это должны быть редкие случаи, так как мы не рекомендуем использовать исключения в качестве механизма управления потоками для обеспечения требуемых условий (чему и посвящена эта статья).

Помните о том, что не следует позволять необработанному исключению не учитывать контекст `noexcept`. В таком случае среда выполнения C++ прекращает процесс с помощью**std::terminate**, теряя при этом все сведения об исключении заполнения, которые собирает C++/WinRT.

## <a name="assertions"></a>Проверочные утверждения
Для проверки внутренних предположений в приложении используются проверочные утверждения. Когда это возможно, используйте **static_assert** для проверки во время компиляции. Во время выполнения используйте `WINRT_ASSERT` с логическим выражением. `WINRT_ASSERT` — это макроопределение, которое передается в [_ASSERTE](/cpp/c-runtime-library/reference/assert-asserte-assert-expr-macros).

```cppwinrt
WINRT_ASSERT(pos < size());
```

В сборках выпуска WINRT_ASSERT компилируется, а в отладочной сборке это утверждение останавливает приложение в отладчике на строке кода, где находится проверочное утверждение.

Не следует использовать исключения в деструкторах. Таким образом, по крайней мере в отладочных сборках вы можете использовать проверочное утверждение результата вызова функции из деструктора с помощью WINRT_VERIFY (с логическим выражением) и WINRT_VERIFY_ (с ожидаемым результатом и логическим выражением).

```cppwinrt
WINRT_VERIFY(::CloseHandle(value));
WINRT_VERIFY_(TRUE, ::CloseHandle(value));
```

## <a name="important-apis"></a>Важные API
* [Шаблон функции winrt::check_bool](/uwp/cpp-ref-for-winrt/error-handling/check-bool)
* [Функция winrt::check_hresult](/uwp/cpp-ref-for-winrt/error-handling/check-hresult)
* [Шаблон функции winrt::check_nt](/uwp/cpp-ref-for-winrt/error-handling/check-nt)
* [Шаблон функции winrt::check_pointer](/uwp/cpp-ref-for-winrt/error-handling/check-pointer)
* [Шаблон функции winrt::check_win32](/uwp/cpp-ref-for-winrt/error-handling/check-win32)
* [Структура winrt::handle](/uwp/cpp-ref-for-winrt/handle)
* [Структура winrt::hresult_error](/uwp/cpp-ref-for-winrt/error-handling/hresult-error)
* [Функция winrt::throw_hresult](/uwp/cpp-ref-for-winrt/error-handling/throw-hresult)
* [Функция winrt::throw_last_error](/uwp/cpp-ref-for-winrt/error-handling/throw-last-error)
* [Функция winrt::to_hresult](/uwp/cpp-ref-for-winrt/error-handling/to-hresult)

## <a name="related-topics"></a>Статьи по теме
* [Обработка ошибок и исключений (современный C++)](/cpp/cpp/errors-and-exception-handling-modern-cpp)
* [How to: Design for Exception Safety](/cpp/cpp/how-to-design-for-exception-safety) (Практическое руководство. Разработка с учетом безопасности исключений)
