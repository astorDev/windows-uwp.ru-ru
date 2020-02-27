---
title: Краткое руководство по обработке трассировки — .NET Трацепроцессинг
description: В этом кратком руководстве вы узнаете, как получить доступ к данным в трассировке ETW.
author: maiak
ms.author: maiak
ms.date: 02/20/2020
ms.topic: quickstart
ms.openlocfilehash: 162646baff9b2d08f6fc0ea4862802216cff9619
ms.sourcegitcommit: 4fdab7be28aca18cb3879fc205eb49edc4f9a96b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77629106"
---
# <a name="quickstart-process-your-first-trace"></a>Краткое руководство. обработка первой трассировки

Испытайте Трацепроцессор для доступа к данным в трассировке событий для трассировки Windows (ETW). Трацепроцессор позволяет получать доступ к данным трассировки ETW в виде объектов .NET.

В этом кратком руководстве вы узнаете, как:

1. Установите пакет NuGet Трацепроцессинг.
2. Создайте Трацепроцессор.
3. Используйте Трацепроцессор для доступа к командным строкам процесса, содержащимся в трассировке.

## <a name="prerequisites"></a>Предварительные требования

Visual Studio 2019

## <a name="install-the-traceprocessing-nuget-package"></a>Установка пакета NuGet Трацепроцессинг

.NET Трацепроцессинг доступен в [NuGet](https://www.nuget.org/packages/Microsoft.Windows.EventTracing.Processing.All) с помощью следующего идентификатора пакета:

Microsoft. Windows. ЕвенттраЦинг. обработка. ALL

Этот пакет можно использовать в консольном приложении для перечисления командных строк процесса, содержащихся в трассировке ETW (ETL-файл).

1. Создайте новое консольное приложение .NET Core. В Visual Studio выберите файл, создать, проект... и выберите шаблон Консольное приложение (.NET Core) для C#.

    Введите имя проекта, например Трацепроцессоркуиккстарт, и нажмите кнопку Создать.

2. В обозреватель решений щелкните правой кнопкой мыши элемент зависимости и выберите пункт Управление пакетами NuGet... и перейдите на вкладку Обзор.

3. В поле поиска введите Microsoft. Windows. ЕвенттраЦинг. обработка. ALL и выполните поиск.

    Выберите установить в пакете NuGet с таким именем и закройте окно NuGet.

## <a name="create-a-traceprocessor"></a>Создание Трацепроцессор

1. Измените Program.cs на следующее содержимое:

    ```csharp
    using Microsoft.Windows.EventTracing;
    using Microsoft.Windows.EventTracing.Processes;
    using System;

    class Program
    {
        static void Main(string[] args)
        {
            if (args.Length != 1)
            {
                Console.Error.WriteLine("Usage: <trace.etl>");
                return;
            }

            using (ITraceProcessor trace = TraceProcessor.Create(args[0]))
            {
                // TODO: call trace.Use...

                trace.Process();

                Console.WriteLine("TODO: Access data from the trace");
            }
        }
    }
    ```

2. Укажите имя трассировки, используемое при запуске проекта.

    В обозреватель решений щелкните правой кнопкой мыши проект и выберите пункт Свойства. Перейдите на вкладку Отладка и введите путь к трассировке (ETL-файл) в аргументах приложения.

    Если у вас еще нет файла трассировки, его можно создать с помощью [средства записи производительности Windows](https://docs.microsoft.com/windows-hardware/test/wpt/start-a-recording) .

3. Запустите приложение.

    Выберите Отладка, начать без отладки, чтобы запустить код.

## <a name="use-traceprocessor-to-access-process-command-lines-contained-in-the-trace"></a>Использование Трацепроцессор для доступа к командным строкам процесса, содержащимся в трассировке

1. Измените Program.cs на следующее содержимое:

    ```csharp
    using Microsoft.Windows.EventTracing;
    using Microsoft.Windows.EventTracing.Processes;
    using System;

    class Program
    {
        static void Main(string[] args)
        {
            if (args.Length != 1)
            {
                Console.Error.WriteLine("Usage: <trace.etl>");
                return;
            }

            using (ITraceProcessor trace = TraceProcessor.Create(args[0]))
            {
                IPendingResult<IProcessDataSource> pendingProcessData = trace.UseProcesses();

                trace.Process();

                IProcessDataSource processData = pendingProcessData.Result;

                foreach (IProcess process in processData.Processes)
                {
                    Console.WriteLine(process.CommandLine);
                }
            }
        }
    }
    ```

2. Запустите приложение еще раз.

    На этот раз вы увидите командные строки списка из всех процессов, которые выполнялись во время записи трассировки.

## <a name="next-steps"></a>Следующие шаги

В этом кратком руководстве вы создали консольное приложение, установили Трацепроцессор и использовали его для доступа к командным строкам процесса трассировки ETW. Теперь у вас есть приложение, обращающееся к данным трассировки.

Сведения о процессе — это лишь один из множества типов данных, которые хранятся в трассировке ETW, к которой имеет доступ приложение.

Следующим шагом является более [детальный взгляд на трацепроцессор](tutorial.md) и другие источники данных, к которым он может получить доступ.
