---
description: В этой статье указаны все технические особенности переноса исходного кода из проекта [C#](/visualstudio/get-started/csharp) в его эквивалент в [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt).
title: Переход на C++/WinRT с C#
ms.date: 07/15/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, port, migrate, C#
ms.localizationpriority: medium
ms.openlocfilehash: 804c22b782dada9c0bde3c379ebfe5a37f1dcff9
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81759947"
---
# <a name="move-to-cwinrt-from-c"></a>Переход на C++/WinRT с C#

В этой статье указаны все технические особенности переноса исходного кода из проекта [C#](/visualstudio/get-started/csharp) в его эквивалент в [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt).

Пример переноса одного из образцов приложений универсальной платформы Windows (UWP) см. в статье [Перенос примера буфера обмена в C++/WinRT из C# — пример использования](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp). Вы можете получить рекомендации и действия по переносу, следуя пошаговому руководству и выполняя перенос образца.

## <a name="how-to-prepare-and-what-to-expect"></a>Подготовка и предполагаемая работа

В этом примере [переноса примера буфера обмена в C++/WinRT из C#](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp) показаны примеры решений по проектированию программного обеспечения, которые будут приняты при переносе проекта в C++/WinRT. Поэтому рекомендуется подготовиться к переносу и хорошо понимать работу имеющегося кода. Вы получите хорошее представление о функциональных возможностях приложения, а также о структуре кода. Таким образом решения, которые вы сможете принимать, будут осознанными и продуктивными.

Изменения, которые следует ожидать от переноса, можно сгруппировать в четыре категории.

- [**Перенос языковой проекции**](#port-the-language-projection). Среда выполнения Windows (WinRT) *проецируется* на различные языки программирования. Каждая из этих языковых проекций разработана так, чтобы быть идиоматической для рассматриваемых языков программирования. Для C# некоторые типы среды выполнения Windows проецируются как типы .NET. Например, вы будете преобразовывать [**System.Collections.Generic.IReadOnlyList\<T\>** ](/dotnet/api/system.collections.generic.ireadonlylist-1) обратно в [**Windows.Foundation.Collections.IVectorView\<T\>** ](/uwp/api/windows.foundation.collections.ivectorview-1). Кроме того, в C# некоторые операции среды выполнения Windows проецируются как удобные функции языка C#. В качестве примера в C# для регистрации делегатов обработки событий вы используете синтаксис оператора `+=`. Поэтому вы будете преобразовывать языковые функции, например, обратно в основную выполняемую операцию (в этом примере — регистрация событий).
- [**Перенос синтаксиса языка**](#port-language-syntax) Многие из этих изменений являются простыми механическими преобразованиями, заменяющими один символ на другой. Например, изменение точки (`.`) на двойное двоеточие (`::`).
- [**Перенос процедуры языка**](#port-language-procedure). Некоторые из них могут быть простыми и повторяющимися изменениями (например, с `myObject.MyProperty` на `myObject.MyProperty()`). Другие нуждаются в более глубоких изменениях (например, перенос процедуры, которая включает использование **System.Text.StringBuilder**, в процедуру, использующую **std::wostringstream**).
- [**Перенос задач, относящихся к C++/WinRT**](#porting-tasks-that-are-specific-to-cwinrt). Некоторые сведения среды выполнения Windows обрабатываются с помощью C# в фоновом режиме. В C++/WinRT эти сведения обрабатываются явно. В качестве примера можно использовать файл `.idl` для определения классов среды выполнения.

Оставшаяся часть этого раздела структурирована в соответствии с этой классификацией.

## <a name="port-the-language-projection"></a>Перенос языковой проекции

||C#|C++/WinRT|См. также статью|
|-|-|-|-|
|Нетипизированный объект|`object` или [**System.Object**](/dotnet/api/system.object)|[**Windows::Foundation::IInspectable**](/windows/win32/api/inspectable/nn-inspectable-iinspectable)|[Перенос метода **EnableClipboardContentChangedNotifications**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#enableclipboardcontentchangednotifications)|
|Пространства имен проекции|`using System;`|`using namespace Windows::Foundation;`||
||`using System.Collections.Generic;`|`using namespace Windows::Foundation::Collections;`||
|Размер коллекции|`collection.Count`|`collection.Size()`|[Перенос метода **BuildClipboardFormatsOutputString**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#buildclipboardformatsoutputstring)|
|Коллекция, доступная только для чтения|[**IReadOnlyList\<T\>** ](/dotnet/api/system.collections.generic.ireadonlylist-1)|[**IVectorView\<T\>** ](/uwp/api/windows.foundation.collections.ivectorview-1)|[Перенос метода **BuildClipboardFormatsOutputString**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#buildclipboardformatsoutputstring)|
|Делегат обработчика событий как член класса|`myObject.EventName += Handler;`|`token = myObject.EventName({ get_weak(), &Class::Handler });`|[Перенос метода **EnableClipboardContentChangedNotifications**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#enableclipboardcontentchangednotifications)|
|Отзыв делегата обработчика событий|`myObject.EventName -= Handler;`|`myObject.EventName(token);`|[Перенос метода **EnableClipboardContentChangedNotifications**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#enableclipboardcontentchangednotifications)|
|Ассоциативный контейнер|[**IDictionary\<K, V\>** ](/dotnet/api/system.collections.generic.idictionary-2)|[**IMap\<K, V\>** ](/uwp/api/windows.foundation.collections.imap-2)||
|Доступ к векторному элементу|`x = v[i];`<br>`v[i] = x;`|`x = v.GetAt(i);`<br>`v.SetAt(i, x);`||

### <a name="registerrevoke-an-event-handler"></a>Регистрация или отзыв обработчика событий

В C++/WinRT есть несколько синтаксических параметров для регистрации или отзыва делегата обработчика событий, как описано в статье [Обработка событий с помощью делегатов в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/handle-events). Ознакомьтесь также с разделом о [переносе метода **EnableClipboardContentChangedNotifications**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#enableclipboardcontentchangednotifications)

Иногда, например, когда получатель события (объект, обрабатывающий событие) будет уничтожен, необходимо отозвать обработчик событий, чтобы источник события (объект, создающий событие) не вызывал уничтоженный объект. Ознакомьтесь с разделом [Отзыв зарегистрированного делегата](/windows/uwp/cpp-and-winrt-apis/handle-events#revoke-a-registered-delegate). В подобных случаях создайте переменную-член **event_token** для ваших обработчиков событий. Дополнительные сведения см. в разделе о [переносе метода **EnableClipboardContentChangedNotifications**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#enableclipboardcontentchangednotifications)

Обработчик событий можно зарегистрировать в разметке XAML.

```xaml
<Button x:Name="OpenButton" Click="OpenButton_Click" />
```

В C# метод **OpenButton_Click** может быть закрытым, но XAML все равно сможет подключать его к событию [**ButtonBase.Click**](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click), вызываемому *OpenButton*.

В C++/WinRT метод **OpenButton_Click** должен быть открытым в вашем [типе имплементации](/windows/uwp/cpp-and-winrt-apis/author-apis), *если нужно зарегистрировать его в разметке XAML*. Если обработчик событий регистрируется только в императивном коде, он не должен быть открытым.

```cppwinrt
namespace winrt::MyProject::implementation
{
    struct MyPage : MyPageT<MyPage>
    {
        void OpenButton_Click(
            winrt::Windows:Foundation::IInspectable const& sender,
            winrt::Windows::UI::Xaml::RoutedEventArgs const& args);
    }
};
```

Кроме того, можно сделать регистрирующую страницу XAML открытой для типа реализации, а **OpenButton_Click** — закрытым.

```cppwinrt
namespace winrt::MyProject::implementation
{
    struct MyPage : MyPageT<MyPage>
    {
    private:
        friend MyPageT;
        void OpenButton_Click(
            winrt::Windows:Foundation::IInspectable const& sender,
            winrt::Windows::UI::Xaml::RoutedEventArgs const& args);
    }
};
```

## <a name="port-language-syntax"></a>Перенос синтаксиса языка

||C#|C++/WinRT|См. также статью|
|-|-|-|-|
|Модификаторы доступа|`public \<member\>`|`public:`<br>&nbsp;&nbsp;&nbsp;&nbsp;`\<member\>`|[Перенос метода **Button_Click**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#button_click)|
|Асинхронное действие|`async Task ...`|`IAsyncAction ...`||
|Асинхронная операция|`async Task<T> ...`|`IAsyncOperation<T> ...`||
|Метод "Выполнил и забыл" (асинхронный)|`async void ...`|`winrt::fire_and_forget ...`|[Перенос метода **CopyButton_Click**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#copybutton_click)|
|Совместное ожидание|`await ...`|`co_await ...`|[Перенос метода **CopyButton_Click**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#copybutton_click)|
|Доступ к константе перечислимого типа|`E.Value`|`E::Value`|[Перенос метода **DisplayChangedFormats**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#displaychangedformats)|
|Разделитель пространства имен|`A.B.T`|`A::B::T`||
|Null|`null`|`nullptr`|[Перенос метода **UpdateStatus**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#updatestatus)|
|Объявление параметра для метода|`MyType`|`MyType const&`|[Передача параметров](/windows/uwp/cpp-and-winrt-apis/concurrency#parameter-passing)|
|Объявление параметра для асинхронного метода|`MyType`|`MyType`|[Передача параметров](/windows/uwp/cpp-and-winrt-apis/concurrency#parameter-passing)|
|Вызов статического метода|`T.Method()`|`T::Method()`||
|Строки|`string` или **System.String**|[**winrt::hstring**](/uw/cpp-ref-for-winrt/hstring)|[Обработка строк в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/strings)|
|Строковый литерал|`"a string literal"`|`L"a string literal"`|[Перенос конструктора **Current** и **FEATURE_NAME**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#the-constructor-current-and-feature_name)|
|Буквальный и необработанный строковый литерал|`@"verbatim string literal"`|`LR"(raw string literal)"`|[Перенос метода **DisplayToast**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp##displaytoast)|
|Доступ к элементу данных|`this.variable`|`this->variable`||
|Директива Using|`using A.B.C;`|`using namespace A::B::C;`|[Перенос конструктора **Current** и **FEATURE_NAME**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#the-constructor-current-and-feature_name)|
|Выводимый (или выведенный) тип|`var`|`auto`|[Перенос метода **BuildClipboardFormatsOutputString**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#buildclipboardformatsoutputstring)|

> [!NOTE]
> Если файл заголовка не содержит директиву `using namespace` для данного пространства имен, то необходимо полностью уточнить все имена типов этого пространства имен или, по крайней мере, определить их должным образом, чтобы компилятор их нашел. Пример см. в разделе [Перенос метода **DisplayToast**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp##displaytoast)

### <a name="porting-classes-and-members"></a>Перенос классов и членов

Вам нужно будет решить для каждого типа C#, следует ли переносить его в тип среды выполнения Windows или в обычный класс, структуру, перечисление C++. Дополнительные сведения и подробные примеры, иллюстрирующие принятие этих решений, см. в статье [Перенос примера буфера обмена в C++/WinRT из C# —пример использования](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp).

Свойство C# обычно становится функцией доступа, функцией метода изменения и резервным членом данных. Дополнительные сведения и пример см. в разделе [Перенос свойства **IsClipboardContentChangedEnabled**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#isclipboardcontentchangedenabled).

Нестатические поля следует сделать членами данных вашего [типа реализации](/windows/uwp/cpp-and-winrt-apis/author-apis).

Статическое поле C# становится функцией статического доступа или функцией метода изменения C++/WinRT. Дополнительные сведения и пример см. в разделе [Перенос конструктора, **Current** и **FEATURE_NAME**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#the-constructor-current-and-feature_name).

Для функций-членов также необходимо решить, относится ли функция к IDL или является общедоступной или закрытой функцией-членом вашего типа реализации. Дополнительные сведения и примеры выбора см. в разделе [IDL для типа **MainPage**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#idl-for-the-mainpage-type).

### <a name="porting-xaml-markup-and-asset-files"></a>Перенос разметки XAML и файлов ресурсов

В статье[Перенос примера буфера обмена в C++/WinRT из C# — пример использования](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp) мы могли использовать *ту же* разметку XAML (включая ресурсы) и файлы ресурсов в проекте C# и C++/WinRT. В некоторых случаях для переноса разметки может потребоваться внести в нее изменения. См. раздел [Копирование XAML и стилей, необходимых для завершения переноса **MainPage**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#copy-the-xaml-and-styles-necessary-to-finish-up-porting-mainpage).

## <a name="port-language-procedure"></a>Перенос процедуры языка

||C#|C++/WinRT|См. также статью|
|-|-|-|-|
|Управление жизненным циклом в асинхронном методе|Н/Д|`auto lifetime{ get_strong() };` или<br>`auto lifetime = get_strong();`|[Перенос метода **CopyButton_Click**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#copybutton_click)|
|Освобождение|`using (var t = v)`|`auto t{ v };`<br>`t.Close(); // or let wrapper destructor do the work`|[Перенос метода **CopyImage**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#copyimage)|
|Создание объекта|`new MyType(args)`|`MyType{ args }` или<br>`MyType(args)`|[Перенос свойства **Scenarios**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#scenarios)|
|Создание неинициализированной ссылки|`MyType myObject;`|`MyType myObject{ nullptr };` или<br>`MyType myObject = nullptr;`|[Перенос конструктора **Current** и **FEATURE_NAME**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#the-constructor-current-and-feature_name)|
|Создание объекта в переменной с аргументами|`var myObject = new MyType(args);`|`auto myObject{ MyType{ args } };` или <br>`auto myObject{ MyType(args) };` или <br>`auto myObject = MyType{ args };` или <br>`auto myObject = MyType(args);` или <br>`MyType myObject{ args };` или <br>`MyType myObject(args);`|[Перенос метода **Footer_Click**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#footer_click)|
|Создание объекта в переменной без аргументов|`var myObject = new T();`|`MyType myObject;`|[Перенос метода **BuildClipboardFormatsOutputString**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#buildclipboardformatsoutputstring)|
|Сокращенная форма инициализации объекта|`var p = new FileOpenPicker{`<br>&nbsp;&nbsp;&nbsp;&nbsp;`ViewMode = PickerViewMode.List`<br>`};`|`FileOpenPicker p;`<br>`p.ViewMode(PickerViewMode::List);`||
|Массовая операция с векторами|`var p = new FileOpenPicker{`<br>&nbsp;&nbsp;&nbsp;&nbsp;`FileTypeFilter = { ".png", ".jpg", ".gif" }`<br>`};`|`FileOpenPicker p;`<br>`p.FileTypeFilter().ReplaceAll({ L".png", L".jpg", L".gif" });`|[Перенос метода **CopyButton_Click**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#copybutton_click)|
|Итерация коллекции|`foreach (var v in c)`|`for (auto&& v : c)`|[Перенос метода **BuildClipboardFormatsOutputString**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#buildclipboardformatsoutputstring)|
|Перехватывание исключений|`catch (Exception ex)`|`catch (winrt::hresult_error const& ex)`|[Перенос метода **PasteButton_Click**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#pastebutton_click)|
|Сведения об исключении|`ex.Message`|`ex.message()`|[Перенос метода **PasteButton_Click**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#pastebutton_click)|
|Получение значения свойства|`myObject.MyProperty`|`myObject.MyProperty()`|[Перенос метода **NotifyUser**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#notifyuser)|
|Задание значения свойства|`myObject.MyProperty = value;`|`myObject.MyProperty(value);`||
|Увеличение значения свойства|`myObject.MyProperty += v;`|`myObject.MyProperty(thing.Property() + v);`<br>Для строк используйте конструктор||
|ToString()|`myObject.ToString()`|`winrt::to_hstring(myObject)`|[ToString()](#tostring)|
|Строка языка для строки среды выполнения Windows|Н/Д|`winrt::hstring{ s }`||
|Сборка строк|`StringBuilder builder;`<br>`builder.Append(...);`|`std::wostringstream builder;`<br>`builder << ...;`|[String-building](#string-building)|
|Интерполяция строк|`$"{i++}) {s.Title}"`|[**winrt::to_hstring**](/uwp/cpp-ref-for-winrt/to-hstring) или [**winrt::hstring::operator+** ](/uwp/cpp-ref-for-winrt/hstring#operator-concatenation-operator)|[Перенос метода **OnNavigatedTo**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#onnavigatedto)|
|Пустая строка для сравнения|**System.String.Empty**|[**winrt::hstring::empty**](/uwp/cpp-ref-for-winrt/hstring#hstringempty-function)|[Перенос метода **UpdateStatus**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#updatestatus)|
|Операции со словарем|`map[k] = v; // replaces any existing`<br>`v = map[k]; // throws if not present`<br>`map.ContainsKey(k)`|`map.Insert(k, v); // replaces any existing`<br>`v = map.Lookup(k); // throws if not present`<br>`map.HasKey(k)`||
|Преобразование типов (вызов при сбое)|`(MyType)v`|`v.as<MyType>()`|[Перенос метода **Footer_Click**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#footer_click)|
|Преобразование типов (NULL при сбое)|`v as MyType`|`v.try_as<MyType>()`|[Перенос метода **PasteButton_Click**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#pastebutton_click)|
|Элементы XAML с x:Name — это свойства|`MyNamedElement`|`MyNamedElement()`|[Перенос конструктора **Current** и **FEATURE_NAME**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#the-constructor-current-and-feature_name)|
|Переключение в поток пользовательского интерфейса|**CoreDispatcher.RunAsync**|**CoreDispatcher.RunAsync** или [**winrt::resume_foreground**](/uwp/cpp-ref-for-winrt/resume-foreground)|[Перенос методов **NotifyUser**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#notifyuser) и [HistoryAndRoaming **** ](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#historyandroaming)|

В следующих разделах приводятся более подробные сведения о некоторых элементах в таблице.

### <a name="tostring"></a>ToString()

Типы C# предоставляют метод [Object.ToString](/dotnet/api/system.object.tostring).

```csharp
int i = 2;
var s = i.ToString(); // s is a System.String with value "2".
```

C++/ WinRT не предоставляет эту функцию напрямую, но можно обратиться к ее альтернативам.

```cppwinrt
int i{ 2 };
auto s{ std::to_wstring(i) }; // s is a std::wstring with value L"2".
```

В C++/WinRT также поддерживается [**winrt::to_hstring**](/uwp/cpp-ref-for-winrt/to-hstring) для ограниченного числа типов. Вам нужно добавить перегрузки для любых дополнительных типов, к которым необходимо применить метод stringify.

| Language | Stringify int | Stringify enum |
| - | - | - |
| C# | `string result = "hello, " + intValue.ToString();`<br>`string result = $"hello, {intValue}";` | `string result = "status: " + status.ToString();`<br>`string result = $"status: {status}";` |
| C++/WinRT | `hstring result = L"hello, " + to_hstring(intValue);` | `// must define overload (see below)`<br>`hstring result = L"status: " + to_hstring(status);` |

Если применяется stringify enum, необходимо предоставить реализацию **winrt::to_hstring**.

```cppwinrt
namespace winrt
{
    hstring to_hstring(StatusEnum status)
    {
        switch (status)
        {
        case StatusEnum::Success: return L"Success";
        case StatusEnum::AccessDenied: return L"AccessDenied";
        case StatusEnum::DisabledByPolicy: return L"DisabledByPolicy";
        default: return to_hstring(static_cast<int>(status));
        }
    }
}
```

Такие операции часто неявно используются привязкой данных.

```xaml
<TextBlock>
You have <Run Text="{Binding FlowerCount}"/> flowers.
</TextBlock>
<TextBlock>
Most recent status is <Run Text="{x:Bind LatestOperation.Status}"/>.
</TextBlock>
```

Эти привязки будут выполнять **winrt::to_hstring** свойства привязки. Во втором примере (**StatusEnum**) необходимо предоставить свою перегрузку **winrt::to_hstring**, иначе возникнет ошибка компилятора.

Ознакомьтесь также с разделом о [переносе метода **Footer_Click**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#footer_click).

### <a name="string-building"></a>Сборка строк

Для сборки строк в C# есть встроенный тип [**StringBuilder**](/dotnet/api/system.text.stringbuilder).

| | C# | C++/WinRT |
|-|-|-|
| Сборка строк | `StringBuilder builder;`<br>`builder.Append(...);` | `std::wostringstream builder;`<br>`builder << ...;` |
| Добавление строки среды выполнения Windows с сохранением значений NULL | `builder.Append(s);` | `builder << std::wstring_view{ s };` |
| Добавление новой строки |`builder.Append(Environment.NewLine);` | `builder << std::endl;` |
| Доступ к результату | `s = builder.ToString();` | `ws = builder.str();` |

Смотрите также раздел [Перенос метода **BuildClipboardFormatsOutputString**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#buildclipboardformatsoutputstring) и [Перенос метода **DisplayChangedFormats**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#displaychangedformats)

## <a name="porting-tasks-that-are-specific-to-cwinrt"></a>Перенос задач, относящихся к C++/WinRT

### <a name="define-your-runtime-classes-in-idl"></a>Определение классов среды выполнения в IDL

Ознакомьтесь с разделом [IDL для типа **MainPage**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#idl-for-the-mainpage-type) и [Консолидация `.idl`файлов](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#consolidate-your-idl-files).

### <a name="include-the-cwinrt-windows-namespace-header-files-that-you-need"></a>Включение нужных файлов заголовков пространства имен Windows C++/WinRT.

Каждый раз в C++/WinRT, когда вы хотите использовать тип из пространств имен Windows, включайте соответствующий файл заголовков пространства имен C++/ WinRT для Windows. Пример см. в разделе [Перенос метода **NotifyUser**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#notifyuser)

### <a name="boxing-and-unboxing"></a>Упаковка-преобразование и распаковка-преобразование

В C# автоматически выполняется упаковка-преобразование скалярных значений в объекты. В C++/WinRT необходимо явным образом вызвать функцию [**winrt::box_value**](/uwp/cpp-ref-for-winrt/box-value). В обоих языках распаковка-преобразование выполняется явным образом. См. подробнее об [упаковке-преобразовании и распаковке-преобразовании в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/boxing).

Эти определения используются в следующих таблицах.

| C# | C++/WinRT|
|-|-|
| `int i;` | `int i;` |
| `string s;` | `winrt::hstring s;` |
| `object o;` | `IInspectable o;`|

| Операция | C# | C++/WinRT|
|-|-|-|
| Упаковка-преобразование | `o = 1;`<br>`o = "string";` | `o = box_value(1);`<br>`o = box_value(L"string");` |
| Распаковка-преобразование | `i = (int)o;`<br>`s = (string)o;` | `i = unbox_value<int>(o);`<br>`s = unbox_value<winrt::hstring>(o);` |

В C++/CX и C# вызываются исключения при попытке распаковки-преобразования пустого указателя в тип значения. В C++/WinRT это считается ошибкой программирования, которая приводит к аварийному завершению. Если в C++/WinRT объект имеет не тот тип, который предполагался, используйте функцию [**winrt::unbox_value_or**](/uwp/cpp-ref-for-winrt/unbox-value-or).

| Сценарий | C# | C++/WinRT|
|-|-|-|
| Распаковка-преобразование известного целого числа |`i = (int)o;` | `i = unbox_value<int>(o);` |
| Если o имеет значение NULL | `System.NullReferenceException` | Аварийное завершение |
| Если o не является упакованным целым числом | `System.InvalidCastException` | Аварийное завершение |
| Выполните распаковку-преобразование целого числа, используйте откат при значении NULL; аварийное завершение при других вариантах | `i = o != null ? (int)o : fallback;` | `i = o ? unbox_value<int>(o) : fallback;` |
| Выполните распаковку-преобразование целого числа, если это возможно; используйте откат при других вариантах | `i = as int? ?? fallback;` | `i = unbox_value_or<int>(o, fallback);` |

Пример см. в разделе [Перенос метода **OnNavigatedTo**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#onnavigatedto) и [Перенос **метода Footer_Click**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#footer_click).

#### <a name="boxing-and-unboxing-a-string"></a>Упаковка-преобразование и распаковка-преобразование строки

Строка в некотором роде является и типом значения, и ссылочным типом. В C# и C++/WinRT строки обрабатываются по-разному.

Тип ABI [**HSTRING**](/windows/win32/winrt/hstring) является указателем на строку с учетом ссылок. Но он не является производным от [**IInspectable**](/windows/win32/api/inspectable/nn-inspectable-iinspectable), поэтому с технической точки зрения это не *объект*. Более того, **HSTRING** со значением NULL представляет пустую строку. Упаковка объектов, которые не унаследованы от **IInspectable**, выполняется путем их упаковки в [**IReference\<T\>** ](/uwp/api/windows.foundation.ireference_t_). Среда выполнения Windows при этом предоставляет стандартную реализацию в виде объекта [**PropertyValue**](/uwp/api/windows.foundation.propertyvalue) (настраиваемые типы отображаются как [**PropertyType::OtherType**](/uwp/api/windows.foundation.propertytype)).

C# представляет строку среды выполнения Windows в виде ссылочного типа, хотя C++/WinRT проецирует строку в виде типа значения. Это означает, что упакованная строка со значением NULL может иметь разные представления в зависимости от способа ее получения.

| Поведение | C# | C++/WinRT|
|-|-|-|
| Объявления | `object o;`<br>`string s;` | `IInspectable o;`<br>`hstring s;` |
| Категория типа строки | Ссылочный тип | Тип значения |
| **HSTRING** со значением NULL проецируется как | `""` | `hstring{}` |
| Значение NULL и `""` идентичны? | Нет | Да |
| Допустимость значения NULL | `s = null;`<br>`s.Length` вызывает NullReferenceException | `s = hstring{};`<br>`s.size() == 0` (допустимо) |
| Если присвоить пустую строку объекту | `o = (string)null;`<br>`o == null` | `o = box_value(hstring{});`<br>`o != nullptr` |
| Если присвоить `""` объекту | `o = "";`<br>`o != null` | `o = box_value(hstring{L""});`<br>`o != nullptr` |

Базовые операции упаковки-преобразования и распаковки-преобразования.

| Операция | C# | C++/WinRT|
|-|-|-|
| Упаковка-преобразование строки | `o = s;`<br>Пустая строка преобразуется в непустой объект. | `o = box_value(s);`<br>Пустая строка преобразуется в непустой объект. |
| Распаковка-преобразование известной строки | `s = (string)o;`<br>Пустой объект преобразуется в пустую строку.<br>InvalidCastException, если не является строкой. | `s = unbox_value<hstring>(o);`<br>Сбой пустого объекта.<br>Сбой, если не является строкой. |
| Распаковка-преобразование возможной строки | `s = o as string;`<br>Пустой объект или нестрока преобразуется в пустую строку.<br><br>ИЛИ<br><br>`s = o as string ?? fallback;`<br>Пустая строка или нестрока преобразуется в fallback.<br>Пустая строка сохранена. | `s = unbox_value_or<hstring>(o, fallback);`<br>Пустая строка или нестрока преобразуется в fallback.<br>Пустая строка сохранена. |

### <a name="making-a-class-available-to-the-binding-markup-extension"></a>Предоставление расширению разметки {Binding} доступа к классу

См. подробнее об [использовании расширения разметки {Binding} для привязки данных к типу данных](/windows/uwp/data-binding/data-binding-in-depth#binding-object-declared-using-binding).

### <a name="consuming-objects-from-xaml-markup"></a>Использование объектов из разметки XAML

В проекте C# вы можете использовать закрытые члены и именованные элементы из разметки XAML. Но в C++/WinRT все сущности, используемые при применении [**расширения разметки {x:Bind}** ](/windows/uwp/xaml-platform/x-bind-markup-extension), XAML должны быть общедоступными в IDL.

Кроме того, привязка к логическому значению приводит к отображению `true` или `false` в C#, тогда как в C++/WinRT отображается **Windows.Foundation.IReference`1\<логическое_значение\>** .

Дополнительные сведения и примеры кода см. в разделе [Использование объектов из разметки](/windows/uwp/cpp-and-winrt-apis/binding-property#consuming-objects-from-xaml-markup).

### <a name="making-a-data-source-available-to-xaml-markup"></a>Предоставление разметке XAML доступа к источнику данных

В C++/WinRT версии 2.0.190530.8 и последующих [**winrt::single_threaded_observable_vector**](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector) создает наблюдаемый вектор, который поддерживает как **[IObservableVector](/uwp/api/windows.foundation.collections.iobservablevector_t_)\<T\>** , так и **IObservableVector\<IInspectable\>** . Пример см. в разделе [Перенос свойства **Scenarios**](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp#scenarios)

Вы можете создать **файл Midl (.idl)** аналогичным образом (см. также раздел о [разделении классов среды выполнения на файлы Midl (.idl)](/windows/uwp/cpp-and-winrt-apis/author-apis#factoring-runtime-classes-into-midl-files-idl)).

```idl
namespace Bookstore
{
    runtimeclass BookSku { ... }

    runtimeclass BookstoreViewModel
    {
        Windows.Foundation.Collections.IObservableVector<BookSku> BookSkus{ get; };
    }

    runtimeclass MainPage : Windows.UI.Xaml.Controls.Page
    {
        MainPage();
        BookstoreViewModel MainViewModel{ get; };
    }
}
```

Выполните реализацию следующим образом.

```cppwinrt
// BookstoreViewModel.h
...
struct BookstoreViewModel : BookstoreViewModelT<BookstoreViewModel>
{
    BookstoreViewModel()
    {
        m_bookSkus = winrt::single_threaded_observable_vector<Bookstore::BookSku>();
        m_bookSkus.Append(winrt::make<Bookstore::implementation::BookSku>(L"To Kill A Mockingbird"));
    }
    
    Windows::Foundation::Collections::IObservableVector<Bookstore::BookSku> BookSkus();
    {
        return m_bookSkus;
    }

private:
    Windows::Foundation::Collections::IObservableVector<Bookstore::BookSku> m_bookSkus;
};
...
```

Дополнительные сведения см. в статьях [Элементы управления XAML; привязка к коллекции C++/WinRT](/windows/uwp/cpp-and-winrt-apis/binding-collection) и [Коллекции с C++/WinRT](/windows/uwp/cpp-and-winrt-apis/collections).

### <a name="making-a-data-source-available-to-xaml-markup-prior-to-cwinrt-201905308"></a>Предоставление разметке XAML доступа к источнику данных (в версиях, предшествующих C++/WinRT 2.0.190530.8)

Для привязки данных XAML требуется, чтобы источник элементов реализовывал **[IIterable](/uwp/api/windows.foundation.collections.iiterable_t_)\<IInspectable\>** , а также одну из следующих комбинаций интерфейсов.

- **IObservableVector\<IInspectable\>**
- **IBindableVector** и **INotifyCollectionChanged**
- **IBindableVector** и **IBindableObservableVector**
- **IBindableVector** самостоятельно (не будет реагировать на изменения)
- **IVector\<IInspectable\>**
- **IBindableIterable** (будет выполнять итерацию и сохранять элементы в закрытую коллекцию)

Универсальный интерфейс, такой как **IVector\<T\>** , не может быть обнаружен во время выполнения. У каждого интерфейса **IVector\<T\>** есть свой идентификатор интерфейса (IID), который является функцией **T**. Разработчики могут расширять набор **T** произвольным образом, поэтому очевидно, что коду привязки XAML будет неизвестен весь набор для выполнения запросов к нему. Это ограничение не является проблемой в C#, так как каждый объект CLR, реализующий **IEnumerable\<T\>** , автоматически реализует **IEnumerable**. На уровне ABI это означает, что каждый объект, реализующий **IObservableVector\<T\>** , автоматически реализует **IObservableVector\<IInspectable\>** .

В C++/WinRT такая возможность не предусмотрена. Если класс среды выполнения C++/WinRT реализует **IObservableVector\<T\>** , нельзя ожидать предоставления **IObservableVector\<IInspectable\>** .

Следовательно, предыдущий пример должен выглядеть так.

```idl
...
runtimeclass BookstoreViewModel
{
    // This is really an observable vector of BookSku.
    Windows.Foundation.Collections.IObservableVector<Object> BookSkus{ get; };
}
```

Реализация выполняется следующим образом.

```cppwinrt
// BookstoreViewModel.h
...
struct BookstoreViewModel : BookstoreViewModelT<BookstoreViewModel>
{
    BookstoreViewModel()
    {
        m_bookSkus = winrt::single_threaded_observable_vector<Windows::Foundation::IInspectable>();
        m_bookSkus.Append(winrt::make<Bookstore::implementation::BookSku>(L"To Kill A Mockingbird"));
    }
    
    // This is really an observable vector of BookSku.
    Windows::Foundation::Collections::IObservableVector<Windows::Foundation::IInspectable> BookSkus();
    {
        return m_bookSkus;
    }

private:
    Windows::Foundation::Collections::IObservableVector<Windows::Foundation::IInspectable> m_bookSkus;
};
...
```

Чтобы получить доступ к объектам в *m_bookSkus*, необходимо применить к ним метод QI в **Bookstore::BookSku**.

```cppwinrt
Widget MyPage::BookstoreViewModel(winrt::hstring title)
{
    for (auto&& obj : m_bookSkus)
    {
        auto bookSku = obj.as<Bookstore::BookSku>();
        if (bookSku.Title() == title) return bookSku;
    }
    return nullptr;
}
```

### <a name="derived-classes"></a>Производные классы

Чтобы создавать производные классы из класса среды выполнения, базовый класс должен быть *составным*. В отличие от C#, в C++/WinRT нужно выполнять отдельные действия по преобразованию классов в составные. Используйте [ключевое слово unsealed](/uwp/midl-3/intro#base-classes), чтобы указать, что вы хотите использовать класс в виде базового класса.

```idl
unsealed runtimeclass BasePage : Windows.UI.Xaml.Controls.Page
{
    ...
}
runtimeclass DerivedPage : BasePage
{
    ...
}
```

В файле заголовка [типа реализации](/windows/uwp/cpp-and-winrt-apis/author-apis) вам нужно включить файл заголовка базового класса до включения автоматически созданного заголовка для производного класса. В противном случае возникнет проблема, например ошибка с сообщением о недопустимом использовании такого типа в виде выражения.

```cppwinrt
// DerivedPage.h
#include "BasePage.h"       // This comes first.
#include "DerivedPage.g.h"  // Otherwise this header file will produce an error.

namespace winrt::MyNamespace::implementation
{
    struct DerivedPage : DerivedPageT<DerivedPage>
    {
        ...
    }
}
```

## <a name="important-apis"></a>Важные API
* [Пространство имен WinRT](/uwp/cpp-ref-for-winrt/winrt)

## <a name="related-topics"></a>Связанные темы
* [Руководства по C#](/visualstudio/get-started/csharp)
* [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/)
* [Подробно о привязке данных](/windows/uwp/data-binding/data-binding-in-depth)