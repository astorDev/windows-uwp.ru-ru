---
author: DelfCo
description: "Средства и методы отладки и тестирования работы приложений с управлением жизненным циклом процесса."
title: "Средства тестирования и отладки для управления жизненным циклом процесса (PLM)"
translationtype: Human Translation
ms.sourcegitcommit: cbf3d2bb1466ca06e397561ad90d95db28e7136d
ms.openlocfilehash: 0fde4fa22dc6876807e7f7e1c10e7606eee3786d

---

# Средства тестирования и отладки для управления жизненным циклом процесса (PLM)

Одно из основных отличий между приложениями UWP и классическими приложениями состоит в том, что приложения UWP располагаются в контейнере с управлением жизненным циклом процесса (PLM). Приложения UWP можно приостановить, возобновить и завершить на всех платформах с помощью службы брокера среды выполнения. Существуют специальные средства для применения этих операций при тестировании и отладке кода, который их обрабатывает.

## Возможности в Visual Studio 2015

Встроенный отладчик Visual Studio 2015 позволяет анализировать потенциальные проблемы при использовании эксклюзивных возможностей UWP. Вы можете принудительно завершить приложение на различных этапах PLM с помощью панели инструментов **События жизненного цикла**, которая отображается при запуске и отладке приложения.

![Панель инструментов "События жизненного цикла"](images/gs-debug-uwp-apps-001.png)

## Средство PLMDebug

PLMDebug.exe — это программа командной строки, которая позволяет управлять состоянием PLM пакета приложения и поставляется в составе Windows SDK. После установки средство по умолчанию располагается в папке *C:\Program Files (x86)\Windows Kits\10\Debuggers\x64*. 

PLMDebug также позволяет отключить PLM для любого установленного пакета приложения, что необходимо для некоторых отладчиков. После отключения PLM служба брокера среды выполнения не сможет завершить работу приложения, чтобы вы могли его отладить. Чтобы отключить PLM, используйте параметр **/enableDebug**, указав после него *полное имя пакета* приложения UWP (короткое имя, имя семейства пакета или AUMID пакета не подходят):

```
plmdebug /enableDebug [PackageFullName]
```

После развертывания приложения UWP из Visual Studio полное имя пакета отображается в окне вывода. Или же можно получить полное имя пакета, выполнив командлет **Get-AppxPackage** в консоли PowerShell.

![Выполнение Get-AppxPackage](images/gs-debug-uwp-apps-003.png)

При необходимости можно указать абсолютный путь к отладчику, который автоматически запускается при активации вашего пакета приложения. Если вы хотите сделать это с помощью Visual Studio, укажите VSJITDebugger.exe как отладчик. Однако для VSJITDebugger.exe необходимо указать параметр "-p" вместе с кодом процесса (PID) приложения UWP. Поскольку невозможно определить PID приложения UWP заранее, этот сценарий не реализован изначально.

Вы можете обойти это ограничение, написав скрипт или средство, которое отслеживает процесс игры, после чего оболочка запускает VSJITDebugger.exe, передавая PID приложения UWP. В следующем примере кода C# показан простой подход для решений этой задачи.

```
using System.Diagnostics;

namespace VSJITLauncher
{
    class Program
    {
        static void Main(string[] args)
        {
            // Name of UWP process, which can be retrieved via Task Manager.
            Process[] processes = Process.GetProcessesByName(args[0]);

            // Get PID of most recent instance
            // Note the highest PID is arbitrary. Windows may recycle or wrap the PID at any time.
            int highestId = 0;
            foreach (Process detectedProcess in processes)
            {
                if (detectedProcess.Id > highestId)
                    highestId = detectedProcess.Id;
            }

            // Launch VSJITDebugger.exe, which resides in C:\Windows\System32
            ProcessStartInfo startInfo = new ProcessStartInfo("vsjitdebugger.exe", "-p " + highestId);
            startInfo.UseShellExecute = true;

            Process process = new Process();
            process.StartInfo = startInfo;
            process.Start();
        }
    }
}
```

Пример использования в сочетании с PLMDebug:

```
plmdebug /enableDebug 279f7062-ce35-40e8-a69f-cc22c08e0bb8_1.0.0.0_x86__c6sq6kwgxxfcg "\"C:\VSJITLauncher.exe\" Game"
```
где `Game` — имя процесса, а `279f7062-ce35-40e8-a69f-cc22c08e0bb8_1.0.0.0_x86__c6sq6kwgxxfcg` — полное имя пакета приложения UWP.

Обратите внимание, что каждый вызов **/enableDebug** следует позже присоединить к другому вызову PLMDebug с помощью параметра **/disableDebug**. Кроме того, путь к отладчику должен быть абсолютным (относительные пути не поддерживаются).

## Статьи по теме
- [Развертывание и отладка приложений UWP](deploying-and-debugging-uwp-apps.md)
- [Отладка, тестирование и производительность](index.md)



<!--HONumber=Jun16_HO4-->

