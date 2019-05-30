---
title: Передача массивов компоненту среды выполнения Windows
description: В универсальной платформе Windows (UWP) параметры используются для ввода или вывода, но не для того и другого. Это значит, что содержимое массива, который передается методу, а также сам массив предназначены только для ввода или только для вывода.
ms.assetid: 8DE695AC-CEF2-438C-8F94-FB783EE18EB9
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: aedf33b6fab55aeb217a3b41a22b55f1cc5c9c5f
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372506"
---
# <a name="passing-arrays-to-a-windows-runtime-component"></a>Передача массивов компоненту среды выполнения Windows




В универсальной платформе Windows (UWP) параметры используются для ввода или вывода, но не для того и другого. Это значит, что содержимое массива, который передается методу, а также сам массив предназначены только для ввода или только для вывода. Если содержимое массива используется для ввода, метод считывает данные из массива, но не записывает их в него. Если содержимое массива используется для вывода, метод записывает данные в массив, но не читает их из него. Это представляет проблему для параметров массива, поскольку массивы в .NET Framework — это ссылочные типы, а содержимое массива не изменяется, даже если ссылка на массив передается значением (**ByVal** в Visual Basic). В [средстве экспорта метаданных среды выполнения Windows (Winmdexp.exe)](https://docs.microsoft.com/dotnet/framework/tools/winmdexp-exe-windows-runtime-metadata-export-tool) необходимо указать цель использования массива, если она не очевидна из контекста, с помощью атрибута ReadOnlyArrayAttribute или WriteOnlyArrayAttribute для параметра. Использование массива определяется следующим образом.

-   Для возвращаемого значения или выходного параметра (параметр **ByRef** с атрибутом [OutAttribute](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.outattribute?redirectedfrom=MSDN) в Visual Basic) массив всегда предназначен только для вывода. Не применяйте атрибут ReadOnlyArrayAttribute. Атрибут WriteOnlyArrayAttribute разрешен для выходных параметров, но он избыточен.

    > **Внимание**  компилятор Visual Basic не применяет правила только вывода. Ни в коем случае не следует читать данные из выходного параметра, так как он может содержать **Nothing**. Всегда присваивайте новый массив.
 
-   Параметры с модификатором **ref** (**ByRef** в Visual Basic) не разрешены. Winmdexp.exe вызывает ошибку.
-   Для параметра, который передается значением, необходимо указать, предназначено ли содержимое массива для ввода или вывода, применив атрибут [ReadOnlyArrayAttribute](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.windowsruntime.readonlyarrayattribute?redirectedfrom=MSDN) или [WriteOnlyArrayAttribute](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.windowsruntime.writeonlyarrayattribute?redirectedfrom=MSDN). Если указать оба атрибута, возникнет ошибка.

Если метод должен принять массив для ввода, изменить содержимое массива и вернуть его вызывающему объекту, используйте параметр только для чтения для ввода и параметр (или возвращаемое значение) только для записи для вывода. В следующем коде показан один из способов реализации этого шаблона.

> [!div class="tabbedCodeSnippets"]
> ```csharp
> public int[] ChangeArray([ReadOnlyArray()] int[] input)
> {
>     int[] output = input.Clone();
>     // Manipulate the copy.
>     //   ...
>     return output;
> }
> ```
> ```vb
> Public Function ChangeArray(<ReadOnlyArray> input() As Integer) As Integer()
>     Dim output() As Integer = input.Clone()
>     ' Manipulate the copy.
>     '   ...
>     Return output
> End Function
> ```

Мы рекомендуем сразу создать копию входного массива и работать с его копией. Это гарантирует, что метод работает одинаково независимо от того, вызывается ли компонент кодом .NET Framework.

## <a name="using-components-from-managed-and-unmanaged-code"></a>Использование компонентов из управляемого и неуправляемого кода


Параметры с атрибутом ReadOnlyArrayAttribute или WriteOnlyArrayAttribute ведут себя по-разному в зависимости от того, написан ли вызывающий объект на управляемом или неуправляемом коде. Если вызывающий объект написан на неуправляемом коде (расширения компонентов JavaScript или Visual C++), содержимое массива обрабатывается следующим образом.

-   ReadOnlyArrayAttribute: Массив копируется в том случае, когда вызов пересекает границы двоичного интерфейса (ABI) приложения. Элементы при необходимости преобразуются. Поэтому все случайные изменения, вносимые методом в массив только для ввода, не видны вызывающей стороне.
-   WriteOnlyArrayAttribute: Вызванный метод не может вносить никаких предположений о содержимом исходного массива. Например, массив, который получает метод, может быть инициализирован или может содержать значения по умолчанию. Ожидается, что метод установит значения всех элементов в массиве.

Если вызывающий объект написан на управляемом коде, исходный массив доступен вызывающему методу, как и любой вызов метода на платформе .NET Framework. Содержимое массива не изменяется в коде .NET Framework, поэтому изменения, вносимые методом в массиве, видны вызывающей стороне. Необходимо помнить об этой особенности, поскольку она влияет на модульные тесты, создаваемые для компонента среды выполнения Windows. Если тесты написаны на неуправляемом коде, содержимое массива не будет изменяться во время тестирования.

## <a name="related-topics"></a>См. также

* [ReadOnlyArrayAttribute](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.windowsruntime.readonlyarrayattribute?redirectedfrom=MSDN)
* [WriteOnlyArrayAttribute](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.windowsruntime.writeonlyarrayattribute?redirectedfrom=MSDN)
* [Создание компонентов среды выполнения Windows в C# и Visual Basic](creating-windows-runtime-components-in-csharp-and-visual-basic.md)
