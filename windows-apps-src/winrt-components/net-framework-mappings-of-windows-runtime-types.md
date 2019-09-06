---
title: Сопоставления типов среда выполнения Windows в .NET
description: В следующей таблице перечислены сопоставления, которые .NET делает между типами универсальная платформа Windows (UWP) и типами .NET.
ms.assetid: 5317D771-808D-4B97-8063-63492B23292F
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: c035db58fc6aa484f9d47a9af61176a2b05d55ee
ms.sourcegitcommit: d38e2f31c47434cd6dbbf8fe8d01c20b98fabf02
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70393661"
---
# <a name="net-mappings-of-windows-runtime-types"></a>Сопоставления типов среда выполнения Windows в .NET

В следующей таблице перечислены сопоставления, которые .NET делает между типами универсальная платформа Windows (UWP) и типами .NET. В универсальном приложении Windows, написанном с помощью управляемого кода, IntelliSense Visual Studio отображает тип .NET вместо типа UWP. Например, если метод среда выполнения Windows&lt;принимает параметр типа String&gt;IVector, то IntelliSense отображает параметр типа IList&lt;String&gt;. Аналогичным образом в компоненте среда выполнения Windows, написанном с помощью управляемого кода, тип .NET используется в сигнатурах членов. Когда [средство экспорта метаданных Среда выполнения Windows (Winmdexp. exe)](/dotnet/framework/tools/winmdexp-exe-windows-runtime-metadata-export-tool) создает компонент Среда выполнения Windows, тип .NET преобразуется в соответствующий тип UWP.

Большинство типов, имеющих одно и то же имя пространства имен и имя типа в UWP и .NET, являются структурами (или типами, связанными со структурами, такими как перечисления). В UWP структуры не имеют членов, кроме полей, и нуждаются в вспомогательных типах, которые .NET скрывает. Версии .NET этих структур имеют свойства и методы, которые предоставляют функциональные возможности скрытых вспомогательных типов.

## <a name="uwp-types-that-map-to-net-types-with-the-same-name-and-namespace"></a>Типы UWP, которые сопоставляются с типами .NET с одинаковыми именами и пространствами имен

### <a name="in-net-assembly-systemobjectmodeldll"></a>В сборке .NET System. ObjectModel. dll

| Пространство имен | Type |
|-|-|
| Windows.UI.Xaml.Input | ICommand |

### <a name="in-net-assembly-systemruntimewindowsruntimedll"></a>В сборке .NET System. Runtime. WindowsRuntime. dll

| Пространство имен | Type |
|-|-|
| Windows.Foundation | Point |
| Windows.Foundation | Rect |
| Windows.Foundation | Size |
| Windows.UI | Color |

### <a name="in-net-assembly-systemruntimewindowsruntimeuixamldll"></a>В сборке .NET System. Runtime. WindowsRuntime. UI. XAML. dll

| Пространство имен | Type |
|-|-|
| Windows.UI.Xaml | CornerRadius |
| Windows.UI.Xaml | Продолжительность |
| Windows.UI.Xaml | DurationType |
| Windows.UI.Xaml | GridLength |
| Windows.UI.Xaml | GridUnitType |
| Windows.UI.Xaml | Thickness |
| Windows.UI.Xaml.Controls.Primitives | GeneratorPosition |
| Windows.UI.Xaml.Media | Matrix (Матрица) |
| Windows.UI.Xaml.Media.Animation | KeyTime |
| Windows.UI.Xaml.Media.Animation | RepeatBehavior |
| Windows.UI.Xaml.Media.Animation | RepeatBehaviorType |
| Windows.UI.Xaml.Media.Media3D | Matrix3D |

## <a name="uwp-types-that-map-to-net-types-with-a-different-name-andor-namespace"></a>Типы UWP, которые сопоставляются с типами .NET с другим именем и (или) пространством имен

### <a name="in-net-assembly-systemobjectmodeldll"></a>В сборке .NET System. ObjectModel. dll

| Тип или пространство имен UWP | Тип или пространство имен .NET |
|-|-|
| INotifyCollectionChanged (Windows.UI.Xaml.Interop) | INotifyCollectionChanged (System.Collections.Specialized) | 
| NotifyCollectionChangedEventHandler (Windows.UI.Xaml.Interop) | NotifyCollectionChangedEventHandler (System.Collections.Specialized) | 
| NotifyCollectionChangedEventArgs (Windows.UI.Xaml.Interop) | NotifyCollectionChangedEventArgs (System.Collections.Specialized) | 
| NotifyCollectionChangedAction (Windows.UI.Xaml.Interop) | NotifyCollectionChangedAction (System.Collections.Specialized) | 
| INotifyPropertyChanged (Windows.UI.Xaml.Data) | INotifyPropertyChanged (System.ComponentModel) | 
| PropertyChangedEventHandler (Windows.UI.Xaml.Data) | PropertyChangedEventHandler (System.ComponentModel) | 
| PropertyChangedEventArgs (Windows.UI.Xaml.Data) | PropertyChangedEventArgs (System.ComponentModel) | 

### <a name="in-net-assembly-systemruntimedll"></a>В сборке .NET System. Runtime. dll

| Тип или пространство имен UWP | Тип или пространство имен .NET |
|-|-|
| AttributeUsageAttribute (Windows.Foundation.Metadata) | AttributeUsageAttribute (System) |
| AttributeTargets (Windows.Foundation.Metadata) | AttributeTargets (System) |
| DateTime (Windows.Foundation) | DateTimeOffset (System) |
| EventHandler&lt;T&gt; (Windows.Foundation) | EventHandler&lt;T&gt; (System) |
| HResult (Windows.Foundation) | Exception (System) |
| IReference&lt;T&gt; (Windows.Foundation) | Nullable&lt;T&gt; (System) |
| TimeSpan (Windows.Foundation) | TimeSpan (System) |
| Uri (Windows.Foundation) | Uri (System) |
| IClosable (Windows.Foundation) | IDisposable (System) |
| IIterable&lt;T&gt; (Windows.Foundation.Collections) | IEnumerable&lt;T&gt; (System.Collections.Generic) |
| IVector&lt;T&gt; (Windows.Foundation.Collections) | IList&lt;T&gt; (System.Collections.Generic) |
| IVectorView&lt;T&gt; (Windows.Foundation.Collections) | IReadOnlyList&lt;T&gt; (System.Collections.Generic) |
| IMap&lt;K,V&gt; (Windows.Foundation.Collections) | IDictionary&lt;TKey,TValue&gt; (System.Collections.Generic) |
| IMapView&lt;K,V&gt; (Windows.Foundation.Collections) | IReadOnlyDictionary&lt;TKey,TValue&gt; (System.Collections.Generic) |
| IKeyValuePair&lt;K,V&gt; (Windows.Foundation.Collections) | KeyValuePair&lt;TKey,TValue&gt; (System.Collections.Generic) |
| IBindableIterable (Windows.UI.Xaml.Interop) | IEnumerable (System.Collections) |
| IBindableVector (Windows.UI.Xaml.Interop) | IList (System.Collections) |
| TypeName (Windows.UI.Xaml.Interop) | Type (System) |

### <a name="in-net-assembly-systemruntimeinteropserviceswindowsruntimedll"></a>В сборке .NET System. Runtime. InteropServices. WindowsRuntime. dll

| Тип или пространство имен UWP | Тип или пространство имен .NET |
|-|-|
| EventRegistrationToken (Windows.Foundation) | EventRegistrationToken (System.Runtime.InteropServices.WindowsRuntime) |

## <a name="related-topics"></a>См. также

* [Среда выполнения Windows компонентов с C# и Visual Basic](creating-windows-runtime-components-in-csharp-and-visual-basic.md)