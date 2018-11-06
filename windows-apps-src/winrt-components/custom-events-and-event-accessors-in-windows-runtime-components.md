---
author: msatranjr
title: Пользовательские события и методы доступа к событиям в компонентах среды выполнения Windows
description: Поддержка платформы .NET Framework для компонентов среды выполнения Windows позволяет без труда объявлять компоненты событий, скрывая различия между шаблонами событий универсальной платформы Windows (UWP) и платформы .NET Framework.
ms.assetid: 6A66D80A-5481-47F8-9499-42AC8FDA0EB4
ms.author: misatran
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 99e215f382bbfe409ac72d021540a471294634ca
ms.sourcegitcommit: e814a13978f33654d8e995584f4b047cb53e0aef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2018
ms.locfileid: "6050443"
---
# <a name="custom-events-and-event-accessors-in-windows-runtime-components"></a>Пользовательские события и методы доступа к событиям в компонентах среды выполнения Windows



Поддержка платформы .NET Framework для компонентов среды выполнения Windows позволяет без труда объявлять компоненты событий, скрывая различия между шаблонами событий универсальной платформы Windows (UWP) и платформы .NET Framework. Однако при объявлении пользовательских методов доступа к событиям в компоненте среды выполнения Windows необходимо следовать шаблону, используемому в UWP.

## <a name="registering-events"></a>Регистрация событий


При регистрации обработки события в UWP метод доступа add возвращает маркер. Для отмены регистрации передайте этот маркер в метод доступа remove. Это означает, что подписи методов доступа add и remove для событий UWP отличаются от подписей привычных методов доступа.

К счастью, компиляторы Visual Basic и C# упрощают этот процесс. Если вы объявляете событие с помощью пользовательских методов доступа в компоненте среды выполнения Windows, компиляторы автоматически применяют шаблон UWP. Например, если метод доступа add не возвращает маркер, возникает ошибка компилятора. Платформа .NET Framework предоставляет два типа для поддержки реализации:

-   Структура [EventRegistrationToken](https://msdn.microsoft.com/library/windows/apps/windows.foundation.eventregistrationtoken.aspx) представляет токен.
-   Класс [EventRegistrationTokenTable&lt;T&gt;](https://msdn.microsoft.com/library/hh138412.aspx) создает токены и поддерживает сопоставление между токенами и обработчиками событий. Аргумент универсального типа является типом аргумента события. Экземпляр этого класса создается для каждого события при первой регистрации обработчика этого события.

В следующем коде события NumberChanged показан основной шаблон событий UWP. В этом примере конструктор объекта аргумента события, NumberChangedEventArgs, принимает один целочисленный параметр, представляющий измененное числовое значение.

> **Примечание**это же шаблон используется компиляторами для обычных событий, объявляемых в компоненте среды выполнения Windows.

 
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

> **Важные**для обеспечения потокобезопасности поле, которое хранит экземпляр объекта EventRegistrationTokenTable&lt;T&gt; должно быть полем уровня класса. Если это поле является полем уровня класса, метод GetOrCreateEventRegistrationTokenTable гарантирует, что при попытке создания таблицы токенов несколькими потоками все потоки получают один и тот же экземпляр таблицы. Для заданного события все вызовы метода GetOrCreateEventRegistrationTokenTable должны использовать одно и то же поле уровня класса.

Вызов метода GetOrCreateEventRegistrationTokenTable в методе доступа remove и в методе [RaiseEvent](https://msdn.microsoft.com/library/fwd3bwed.aspx) (метод OnRaiseEvent в C#) обеспечивает отсутствие исключений при вызове этих методов до добавления делегатов каких-либо обработчиков событий.

Другие члены класса EventRegistrationTokenTable&lt;T&gt;, используемые в шаблоне событий UWP, включают следующее:

-   Метод [AddEventHandler](https://msdn.microsoft.com/library/hh138458.aspx) создает маркер для делегата обработчика событий, сохраняет делегат в таблице, добавляет его в список вызовов и возвращает маркер.
-   При перегрузке метода [RemoveEventHandler (EventRegistrationToken)](https://msdn.microsoft.com/library/hh138425.aspx) делегат удаляется из таблицы и из списка вызовов.

    >**Примечание**блокировать методы AddEventHandler и removeeventhandler(eventregistrationtoken) блокируют таблицу для обеспечения потокобезопасности.

-   Свойство [InvocationList](https://msdn.microsoft.com/library/hh138465.aspx) возвращает делегат, включающий все обработчики событий, которые в настоящее время зарегистрированы для обработки события. Используйте этот делегат для порождения события или используйте методы класса Delegate для вызова обработчиков по отдельности.

    >**Примечание**мы рекомендуем следовать шаблону, показанному в примере выше в этой статье и копировать делегат перед вызовом во временную переменную. Это позволяет избежать состояния гонки, при котором один поток удаляет последний обработчик, превращая делегат в null непосредственно перед тем, как другой поток попытается вызвать его. Делегаты неизменяемы, поэтому копия остается действительной.

Разместите свой код в методах доступа соответствующим образом. Если потокобезопасность важна, вы должны обеспечить собственную блокировку кода.

Для пользователей C#: при написании пользовательских методов доступа к событиям в соответствии с шаблоном событий UWP компилятор не поддерживает обычные синтаксические сокращения. При использовании в коде имени события компилятор возвращает ошибку.

Для пользователей Visual Basic: на платформе .NET Framework событие является лишь многоадресным делегатом, представляющим все зарегистрированные обработчики событий. Порождение события означает лишь вызов делегата. Синтаксис Visual Basic обычно скрывает взаимодействия с делегатом, а компилятор копирует делегат перед его вызовом, как сказано в примечании о потокобезопасности. При создании пользовательского события в компоненте среды выполнения Windows необходимо работать с делегатом напрямую. Это также означает, что вы можете, например, с помощью метода [MulticastDelegate.GetInvocationList](https://msdn.microsoft.com/library/system.multicastdelegate.getinvocationlist.aspx) получать массив, содержащий отдельный делегат для каждого обработчика событий, если требуется вызывать обработчики отдельно.

## <a name="related-topics"></a>Ссылки по теме

* [События (Visual Basic)](https://msdn.microsoft.com/library/ms172877.aspx)
* [События (руководство по программированию в C#)](https://msdn.microsoft.com/library/awbftdfh.aspx)
* [Обзор .NET для UWP приложений](https://msdn.microsoft.com/library/windows/apps/xaml/br230302.aspx)
* [Платформа .NET для приложений UWP](https://msdn.microsoft.com/library/windows/apps/xaml/mt185501.aspx)
* [Пошаговое руководство. Создание простого компонента среды выполнения Windows и его вызов из кода JavaScript](walkthrough-creating-a-simple-windows-runtime-component-and-calling-it-from-javascript.md)
