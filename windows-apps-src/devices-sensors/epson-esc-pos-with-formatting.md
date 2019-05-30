---
ms.assetid: 70667353-152B-4B18-92C1-0178298052D4
title: Epson ESC/POS с форматированием
description: Узнайте, как использовать командный язык ESC/POS для форматирования текста (например, изменения начертания на полужирное или увеличения размера символов в два раза) для принтера точки обслуживания.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 0731f551afaa2420451521b186515255c3724c36
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66370144"
---
# <a name="epson-escpos-with-formatting"></a>Epson ESC/POS с форматированием


**Важные API**

-   [**PointofService принтера**](https://docs.microsoft.com/uwp/api/Windows.Devices.PointOfService)
-   [**Windows.Devices.PointOfService**](https://docs.microsoft.com/uwp/api/Windows.Devices.PointOfService)

Узнайте, как использовать командный язык ESC/POS для форматирования текста (например, изменения начертания на полужирное или увеличения размера символов в два раза) для принтера точки обслуживания.

## <a name="escpos-usage"></a>Использование ESC/POS

Точка обслуживания Windows обеспечивает использование различных принтеров, включая несколько принтеров серии TM марки Epson (полный список поддерживаемых принтеров см. на странице [Принтер PointofService](https://docs.microsoft.com/uwp/api/Windows.Devices.PointOfService)). Windows поддерживает печать с помощью командного языка принтера ESC/POS, который предоставляет эффективные и функциональные команды для взаимодействия с вашим принтером.

ESC/POS — это система управления, созданная Epson и используемая во многих системах принтеров POS с целью исключения несовместимых наборов команд путем обеспечения универсальной применимости. Большинство современных принтеров поддерживают ESC/POS.

Все команды начинаются с символа ESC (ASCII 27, HEX 1B) или GS (ASCII 29, HEX 1D), после которого следует другой символ, указывающий команду. Обычный текст, разделенный разрывами строк, просто отправляется на принтер.

В [**Windows PointOfService API**](https://docs.microsoft.com/uwp/api/Windows.Devices.PointOfService) большинство этих функций используется с помощью метода **Print()** или **PrintLine()** . Но для получения определенного форматирования или отправки определенных команд необходимо воспользоваться командами ESC/POS, созданными в виде строки и отправленными на принтер.

## <a name="example-using-bold-and-double-size-characters"></a>Пример использования полужирных символов и символов двойного размера

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

Подробнее об ESC/POS, в том числе о доступных командах, см. в разделе [Epson ESC/POS — вопросы и ответы](https://content.epson.de/fileadmin/content/files/RSD/downloads/escpos.pdf). Подробнее о [**Windows.Devices.PointOfService**](https://docs.microsoft.com/uwp/api/Windows.Devices.PointOfService) и всех доступных функциях см. в статье [Принтер PointofService](https://docs.microsoft.com/uwp/api/Windows.Devices.PointOfService) на сайте MSDN.
