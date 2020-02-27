---
title: Загрузка символов — .NET Трацепроцессинг
description: В этом руководстве описано, как загружать символы при обработке трассировок.
author: maiak
ms.author: maiak
ms.date: 02/23/2020
ms.topic: tutorial
ms.openlocfilehash: a6954538159c6fffb3185aa8b3137af26e17b32f
ms.sourcegitcommit: 4fdab7be28aca18cb3879fc205eb49edc4f9a96b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77629095"
---
# <a name="use-symbols-in-net-traceprocessing"></a>Использование символов в .NET Трацепроцессинг

[Трацепроцессор](https://docs.microsoft.com/dotnet/api/microsoft.windows.eventtracing.traceprocessor) поддерживает загрузку символов и получение стеков из нескольких источников данных. Следующее консольное приложение выполняет поиск в примерах ЦП и выводит оценочную длительность выполнения определенной функции (в зависимости от статистической выборки загрузки ЦП в трассировке).

```csharp
using Microsoft.Windows.EventTracing;
using Microsoft.Windows.EventTracing.Cpu;
using Microsoft.Windows.EventTracing.Symbols;
using System;
using System.Collections.Generic;

class Program
{
    static void Main(string[] args)
    {
        if (args.Length != 3)
        {
            Console.Error.WriteLine("Usage: GetCpuSampleDuration.exe <trace.etl> <imageName> <functionName>");
            return;
        }

        string tracePath = args[0];
        string imageName = args[1];
        string functionName = args[2];
        Dictionary<string, Duration> matchDurationByCommandLine = new Dictionary<string, Duration>();

        using (ITraceProcessor trace = TraceProcessor.Create(tracePath))
        {
            IPendingResult<ISymbolDataSource> pendingSymbolData = trace.UseSymbols();
            IPendingResult<ICpuSampleDataSource> pendingCpuSamplingData = trace.UseCpuSamplingData();

            trace.Process();

            ISymbolDataSource symbolData = pendingSymbolData.Result;
            ICpuSampleDataSource cpuSamplingData = pendingCpuSamplingData.Result;

            symbolData.LoadSymbolsForConsoleAsync(SymCachePath.Automatic, SymbolPath.Automatic).GetAwaiter().GetResult();

            Console.WriteLine();
            IThreadStackPattern pattern = AnalyzerThreadStackPattern.Parse($"{imageName}!{functionName}");

            foreach (ICpuSample sample in cpuSamplingData.Samples)
            {
                if (sample.Stack != null && sample.Stack.Matches(pattern))
                {
                    string commandLine = sample.Process.CommandLine;

                    if (!matchDurationByCommandLine.ContainsKey(commandLine))
                    {
                        matchDurationByCommandLine.Add(commandLine, Duration.Zero);
                    }

                    matchDurationByCommandLine[commandLine] += sample.Weight;
                }
            }

            foreach (string commandLine in matchDurationByCommandLine.Keys)
            {
                Console.WriteLine($"{commandLine}: {matchDurationByCommandLine[commandLine]}");
            }
        }
    }
}
```

При запуске этой программы создаются выходные данные следующего вида:

```shell
C:\GetCpuSampleDuration\bin\Debug\> GetCpuSampleDuration.exe C:\boot.etl user32.dll LoadImageInternal
0.0% (0 of 1165; 0 loaded)
<snip>
100.0% (1165 of 1165; 791 loaded)
wininit.exe: 15.99 ms
C:\Windows\Explorer.EXE: 5 ms
winlogon.exe: 20.15 ms
"C:\Users\AdminUAC\AppData\Local\Microsoft\OneDrive\OneDrive.exe" /background: 2.09 ms
```

(Выходные данные будут зависеть от трассировки).

## <a name="symbols-format"></a>Формат символов

На внутреннем уровне Трацепроцессор использует формат [симкаче](https://docs.microsoft.com/windows-hardware/test/wpt/loading-symbols#symcache-path) , который представляет собой кэш некоторых данных, хранящихся в PDB-файле. При загрузке символов Трацепроцессор требует указания расположения для этих файлов Симкаче (путь Симкаче) и поддерживает при необходимости указание SymbolPath для доступа к PDB. Когда предоставляется SymbolPath, Трацепроцессор будет создавать файлы Симкаче из PDB-файлов по мере необходимости, а при дальнейшей обработке одних и тех же данных можно использовать файлы Симкаче напрямую для повышения производительности.

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы узнали, как загружать символы при обработке трассировок.

Следующий шаг — Узнайте, как [использовать потоковую передачу](streaming.md) для доступа к данным трассировки без буферизации всех данных в памяти.
