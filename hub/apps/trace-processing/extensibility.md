---
title: Расширяемость — .NET Трацепроцессинг
description: В этом руководстве описано, как расширить Трацепроцессинг .NET.
author: maiak
ms.author: maiak
ms.date: 02/23/2020
ms.topic: overview
ms.openlocfilehash: 59722f1f31364c464a8a763d28f3d15ef13609a8
ms.sourcegitcommit: cfba95a96202c4250de845115d1b99361412a779
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77903291"
---
# <a name="extend-traceprocessor"></a>Расширение Трацепроцессор

Многие виды данных трассировки имеют встроенную поддержку в [трацепроцессор](https://docs.microsoft.com/dotnet/api/microsoft.windows.eventtracing.traceprocessor), но если у вас есть другие поставщики, которые вы хотели бы проанализировать (включая собственные поставщики), эти данные также доступны из трассировки в режиме реального времени при обработке.

> [!NOTE]
> Эта часть API находится на этапе предварительной версии и находится под активной разработкой. В будущих выпусках она может измениться.

Например, вот простой способ получения списка идентификаторов поставщиков в трассировке.

```csharp
// Open a trace with TraceProcessor.Create() and call Run...

static void Run(ITraceProcessor trace)
{
    HashSet<Guid> providerIds = new HashSet<Guid>();
    trace.Use((e) => providerIds.Add(e.ProviderId));
    trace.Process();

    foreach (Guid providerId in providerIds)
    {
        Console.WriteLine(providerId);
    }
}
```

В следующем примере показан упрощенный пользовательский источник данных.

```csharp
// Open a trace with TraceProcessor.Create() and call Run...

static void Run(ITraceProcessor trace)
{
    CustomDataSource customDataSource = new CustomDataSource();
    trace.Use(customDataSource);

    trace.Process();

    Console.WriteLine(customDataSource.Count);
}

class CustomDataSource : IFilteredEventConsumer
{
    public IReadOnlyList<Guid> ProviderIds { get; } = new Guid[] { new Guid("your provider ID") };

    public int Count { get; private set; }

    public void Process(EventContext eventContext)
    {
        ++Count;
    }
}
```

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы узнали, как расширить Трацепроцессор.

Следующий шаг — Узнайте, как [загружать символы](symbols.md) для трассировок.
