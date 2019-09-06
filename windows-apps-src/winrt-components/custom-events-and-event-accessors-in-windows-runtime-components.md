---
title: Пользовательские события и методы доступа к событиям в компонентах среды выполнения Windows
description: Поддержка компонентов среда выполнения Windows в .NET позволяет легко объявлять компоненты событий, скрывая различия между шаблоном событий универсальная платформа Windows (UWP) и шаблоном событий .NET.
ms.assetid: 6A66D80A-5481-47F8-9499-42AC8FDA0EB4
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 4180fc0f3b949ddf231df8b839831c70ce44d634
ms.sourcegitcommit: d38e2f31c47434cd6dbbf8fe8d01c20b98fabf02
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70393720"
---
# <a name="custom-events-and-event-accessors-in-windows-runtime-components"></a>Пользовательские события и методы доступа к событиям в компонентах среды выполнения Windows

Поддержка компонентов среда выполнения Windows в .NET позволяет легко объявлять компоненты событий, скрывая различия между шаблоном событий универсальная платформа Windows (UWP) и шаблоном событий .NET. Однако при объявлении пользовательских методов доступа к среда выполнения Windows компоненту необходимо следовать шаблону, используемому в UWP.

## <a name="registering-events"></a>Регистрация событий

При регистрации обработки события в UWP метод доступа add возвращает маркер. Для отмены регистрации передайте этот маркер в метод доступа remove. Это означает, что подписи методов доступа add и remove для событий UWP отличаются от подписей привычных методов доступа.

К счастью, Visual Basic и C# компиляторы упрощают этот процесс: При объявлении события с пользовательскими методами доступа в компоненте среда выполнения Windows компиляторы автоматически используют шаблон UWP. Например, если метод доступа add не возвращает маркер, возникает ошибка компилятора. .NET предоставляет два типа для поддержки реализации:

-   Структура [EventRegistrationToken](https://docs.microsoft.com/uwp/api/windows.foundation.eventregistrationtoken) представляет токен.
-   Класс [EventRegistrationTokenTable&lt;T&gt;](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.windowsruntime.eventregistrationtokentable-1?redirectedfrom=MSDN) создает токены и поддерживает сопоставление между токенами и обработчиками событий. Аргумент универсального типа является типом аргумента события. Экземпляр этого класса создается для каждого события при первой регистрации обработчика этого события.

В следующем коде события NumberChanged показан основной шаблон событий UWP. В этом примере конструктор объекта аргумента события, NumberChangedEventArgs, принимает один целочисленный параметр, представляющий измененное числовое значение.

> **Обратите внимание**  , что такая же схема используется компиляторами для обычных событий, объявляемых в компоненте Среда выполнения Windows.

 
> [!div class="tabbedCodeSnippets"]
> ```csharp
> private EventRegistrationTokenTable<EventHandler<NumberChangedEventArgs>>
>     m_NumberChangedTokenTable = null;
>
> public event EventHandler<NumberChangedEventArgs> NumberChanged
> {
>     add
>     {
>         return EventRegistrationTokenTable<EventHandler<NumberChangedEventArgs>>
>             .GetOrCreateEventRegistrationTokenTable(ref m_NumberChangedTokenTable)
>             .AddEventHandler(value);
>     }
>     remove
>     {
>         EventRegistrationTokenTable<EventHandler<NumberChangedEventArgs>>
>             .GetOrCreateEventRegistrationTokenTable(ref m_NumberChangedTokenTable)
>             .RemoveEventHandler(value);
>     }
> }
>
> internal void OnNumberChanged(int newValue)
> {
>     EventHandler<NumberChangedEventArgs> temp =
>         EventRegistrationTokenTable<EventHandler<NumberChangedEventArgs>>
>         .GetOrCreateEventRegistrationTokenTable(ref m_NumberChangedTokenTable)
>         .InvocationList;
>     if (temp != null)
>     {
>         temp(this, new NumberChangedEventArgs(newValue));
>     }
> }
> ```
> ```vb
> Private m_NumberChangedTokenTable As  _
>     EventRegistrationTokenTable(Of EventHandler(Of NumberChangedEventArgs))
>
> Public Custom Event NumberChanged As EventHandler(Of NumberChangedEventArgs)
>
>     AddHandler(ByVal handler As EventHandler(Of NumberChangedEventArgs))
>         Return EventRegistrationTokenTable(Of EventHandler(Of NumberChangedEventArgs)).
>             GetOrCreateEventRegistrationTokenTable(m_NumberChangedTokenTable).
>             AddEventHandler(handler)
>     End AddHandler
>
>     RemoveHandler(ByVal token As EventRegistrationToken)
>         EventRegistrationTokenTable(Of EventHandler(Of NumberChangedEventArgs)).
>             GetOrCreateEventRegistrationTokenTable(m_NumberChangedTokenTable).
>             RemoveEventHandler(token)
>     End RemoveHandler
>
>     RaiseEvent(ByVal sender As Class1, ByVal args As NumberChangedEventArgs)
>         Dim temp As EventHandler(Of NumberChangedEventArgs) = _
>             EventRegistrationTokenTable(Of EventHandler(Of NumberChangedEventArgs)).
>             GetOrCreateEventRegistrationTokenTable(m_NumberChangedTokenTable).
>             InvocationList
>         If temp IsNot Nothing Then
>             temp(sender, args)
>         End If
>     End RaiseEvent
> End Event
> ```

Статический метод (общий метод в Visual Basic) GetOrCreateEventRegistrationTokenTable создает экземпляр объекта EventRegistrationTokenTable&lt;T&gt; для события отложенным образом. Передайте в этот метод поле уровня класса, в котором будет храниться экземпляр таблицы токенов. Если поле оставлено пустым, метод создает таблицу, сохраняет ссылку на нее в поле и возвращает ссылку на эту таблицу. Если поле уже содержит ссылку на таблицу маркеров, то метод просто возвращает эту ссылку.

> Важно  . чтобы обеспечить потокобезопасность, поле, содержащее экземпляр события евентрегистратионтокентабле&lt;T&gt; , должно быть полем уровня класса. Если это поле является полем уровня класса, метод GetOrCreateEventRegistrationTokenTable гарантирует, что при попытке создания таблицы токенов несколькими потоками все потоки получают один и тот же экземпляр таблицы. Для заданного события все вызовы метода GetOrCreateEventRegistrationTokenTable должны использовать одно и то же поле уровня класса.

Вызов метода GetOrCreateEventRegistrationTokenTable в методе доступа remove и в методе [RaiseEvent](https://docs.microsoft.com/dotnet/articles/visual-basic/language-reference/statements/raiseevent-statement) (метод OnRaiseEvent в C#) обеспечивает отсутствие исключений при вызове этих методов до добавления делегатов каких-либо обработчиков событий.

Другие члены класса EventRegistrationTokenTable&lt;T&gt;, используемые в шаблоне событий UWP, включают следующее:

-   Метод [AddEventHandler](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.windowsruntime.eventregistrationtokentable-1.addeventhandler?redirectedfrom=MSDN#System_Runtime_InteropServices_WindowsRuntime_EventRegistrationTokenTable_1_AddEventHandler__0_) создает маркер для делегата обработчика событий, сохраняет делегат в таблице, добавляет его в список вызовов и возвращает маркер.
-   При перегрузке метода [RemoveEventHandler (EventRegistrationToken)](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.windowsruntime.eventregistrationtokentable-1.removeeventhandler?redirectedfrom=MSDN#System_Runtime_InteropServices_WindowsRuntime_EventRegistrationTokenTable_1_RemoveEventHandler_System_Runtime_InteropServices_WindowsRuntime_EventRegistrationToken_) делегат удаляется из таблицы и из списка вызовов.

    >**Обратите внимание**  , что методы аддевенсандлер и ремовивенсандлер (EventRegistrationToken) блокируют таблицу для обеспечения потокобезопасности.

-   Свойство [InvocationList](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.windowsruntime.eventregistrationtokentable-1.invocationlist?redirectedfrom=MSDN#System_Runtime_InteropServices_WindowsRuntime_EventRegistrationTokenTable_1_InvocationList) возвращает делегат, включающий все обработчики событий, которые в настоящее время зарегистрированы для обработки события. Используйте этот делегат для порождения события или используйте методы класса Delegate для вызова обработчиков по отдельности.

    >**Обратите внимание**  , что мы рекомендуем следовать шаблону, приведенному в примере, приведенном ранее в этой статье, и скопировать делегат во временную переменную перед вызовом. Это позволяет избежать состояния гонки, при котором один поток удаляет последний обработчик, превращая делегат в null непосредственно перед тем, как другой поток попытается вызвать его. Делегаты неизменяемы, поэтому копия остается действительной.

Разместите свой код в методах доступа соответствующим образом. Если потокобезопасность важна, вы должны обеспечить собственную блокировку кода.

C#пользователей При написании пользовательских методов доступа к событиям в шаблоне событий UWP компилятор не предоставляет обычные синтаксические ярлыки. При использовании в коде имени события компилятор возвращает ошибку.

Visual Basic пользователей: В .NET событие — это просто делегат многоадресной рассылки, представляющий все зарегистрированные обработчики событий. Порождение события означает лишь вызов делегата. Синтаксис Visual Basic обычно скрывает взаимодействия с делегатом, а компилятор копирует делегат перед его вызовом, как сказано в примечании о потокобезопасности. При создании пользовательского события в среда выполнения Windows компоненте необходимо напрямую работать с делегатом. Это также означает, что вы можете, например, с помощью метода [MulticastDelegate.GetInvocationList](https://docs.microsoft.com/dotnet/api/system.multicastdelegate.getinvocationlist?redirectedfrom=MSDN#System_MulticastDelegate_GetInvocationList) получать массив, содержащий отдельный делегат для каждого обработчика событий, если требуется вызывать обработчики отдельно.

## <a name="related-topics"></a>См. также

* [События (Visual Basic)](https://docs.microsoft.com/dotnet/articles/visual-basic/programming-guide/language-features/events/index)
* [События (C# руководством по программированию)](https://docs.microsoft.com/dotnet/articles/csharp/programming-guide/events/index)
* [Обзор .NET для приложений UWP](https://docs.microsoft.com/previous-versions/windows/apps/br230302(v=vs.140))
* [.NET для приложений UWP](https://docs.microsoft.com/dotnet/api/index?view=dotnet-uwp-10.0)
* [Пошаговое руководство C# по созданию компонента Visual Basic среда выполнения Windows и его вызову из JavaScript](walkthrough-creating-a-simple-windows-runtime-component-and-calling-it-from-javascript.md)
