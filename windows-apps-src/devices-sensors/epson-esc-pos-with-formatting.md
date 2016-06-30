---
author: DBirtolo
ms.assetid: 70667353-152B-4B18-92C1-0178298052D4
title: "Epson ESC/POS с форматированием"
description: "Узнайте, как использовать командный язык ESC/POS для форматирования текста (например, изменения начертания на полужирное или увеличения размера символов в два раза) для принтера точки обслуживания."
translationtype: Human Translation
ms.sourcegitcommit: ba620bc89265cbe8756947e1531759103c3cafef
ms.openlocfilehash: 33231abb1e573d837193daeee21713f09c7d8448

---
# Epson ESC/POS с форматированием

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

** Важные API **

-   [Принтер PointofService](https://msdn.microsoft.com/library/windows/apps/Mt426652)
-   [**Windows.Devices.PointOfService**](https://msdn.microsoft.com/library/windows/apps/Dn298071)

Узнайте, как использовать командный язык ESC/POS для форматирования текста (например, изменения начертания на полужирное или увеличения размера символов в два раза) для принтера точки обслуживания.

## Использование ESC/POS

Точка обслуживания Windows обеспечивает использование различных принтеров, включая несколько принтеров серии TM марки Epson (полный список поддерживаемых принтеров см. на странице [Принтер PointofService](https://msdn.microsoft.com/library/windows/apps/Mt426652)). Windows поддерживает печать с помощью командного языка принтера ESC/POS, который предоставляет эффективные и функциональные команды для взаимодействия с вашим принтером.

ESC/POS — это система управления, созданная Epson и используемая во многих системах принтеров POS с целью исключения несовместимых наборов команд путем обеспечения универсальной применимости. Большинство современных принтеров поддерживают ESC/POS.

Все команды начинаются с символа ESC (ASCII 27, HEX 1B) или GS (ASCII 29, HEX 1D), после которого следует другой символ, указывающий команду. Обычный текст, разделенный разрывами строк, просто отправляется на принтер.

В [**Windows PointOfService API**](https://msdn.microsoft.com/library/windows/apps/Dn298071) большинство этих функций используется с помощью метода **Print()** или **PrintLine()**. Но для получения определенного форматирования или отправки определенных команд необходимо воспользоваться командами ESC/POS, созданными в виде строки и отправленными на принтер.

## Пример использования полужирных символов и символов двойного размера

В примере ниже показано использование команд ESC/POS для печати полужирных символов и символов двойного размера. Обратите внимание, что каждая команда создается в виде строки, а затем вставляется в вызовы заданий печати.

```csharp
// … prior plumbing code removed for brevity
// this code assumed you've already created a receipt print job (printJob)
// and also that you've already checked the PosPrinter Capabilities to 
// verify that the printer supports Bold and DoubleHighDoubleWide print modes

const string ESC = "\u001B";
const string GS = "\u001D";
const string InitializePrinter = ESC + "@";
const string BoldOn = ESC + "E" + "\u0001";
const string BoldOff = ESC + "E" + "\0";
const string DoubleOn = GS + "!" + "\u0011";  // 2x sized text (double-high + double-wide)
const string DoubleOff = GS + "!" + "\0";

printJob.Print(InitializePrinter);
printJob.PrintLine("Here is some normal text.");
printJob.PrintLine(BoldOn + "Here is some bold text." + BoldOff);
printJob.PrintLine(DoubleOn + "Here is some large text." + DoubleOff);

printJob.ExecuteAsync();
```

Подробнее об ESC/POS, в том числе о доступных командах, см. в разделе [Epson ESC/POS — вопросы и ответы](http://content.epson.de/fileadmin/content/files/RSD/downloads/escpos.pdf). Подробнее о [**Windows.Devices.PointOfService**](https://msdn.microsoft.com/library/windows/apps/Dn298071) и всех доступных функциях см. в статье [Принтер PointofService](https://msdn.microsoft.com/library/windows/apps/Mt426652) на сайте MSDN.





<!--HONumber=Jun16_HO4-->


