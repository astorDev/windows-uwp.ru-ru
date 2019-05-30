---
title: Сопоставление типов .NET Framework с типами среды выполнения Windows
description: В следующей таблице перечислены установленные в .NET Framework сопоставления между типами универсальной платформы Windows (UWP) и типами .NET Framework.
ms.assetid: 5317D771-808D-4B97-8063-63492B23292F
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: dc9d71f6f02ec62fdf96161c397f7d2d7363b506
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372513"
---
# <a name="net-framework-mappings-of-windows-runtime-types"></a>Сопоставление типов .NET Framework с типами среды выполнения Windows



В следующей таблице перечислены установленные в .NET Framework сопоставления между типами универсальной платформы Windows (UWP) и типами .NET Framework. В универсальном приложении для Windows, написанном с использованием управляемого кода, IntelliSense отображает тип .NET Framework вместо типа UWP. Например, если метод среды выполнения Windows принимает параметр типа IVector&lt;string&gt;, IntelliSense отображает параметр типа IList&lt;string&gt;. Аналогичным образом в компоненте среды выполнения Windows, написанном с использованием управляемого кода, в сигнатурах членов используются типы .NET Framework. Когда [средство экспорта метаданных среды выполнения Windows (Winmdexp.exe)](https://docs.microsoft.com/dotnet/framework/tools/winmdexp-exe-windows-runtime-metadata-export-tool) создает компонент среды выполнения Windows, тип .NET Framework становится соответствующим типом UWP.

## <a name="mapping-tables"></a>Таблицы соответствия


Большинство типов, имеющих одинаковые имена пространств имен и типов в UWP и .NET Framework, являются структурами (или типами, связанными со структурами, например перечислениями). В UWP структуры не имеют членов, кроме полей, и для них требуются вспомогательные типы, скрываемые платформой .NET Framework. Соответствующие структуры версии .NET Framework имеют свойства и методы, предоставляющие функциональность скрытых вспомогательных типов.

Таблица 1. UWP типы, сопоставленные с типами .NET Framework с другим именем или пространством имен.

| Тип или пространство имен UWP                                            | Тип или пространство имен .NET Framework                                          | Сборка .NET Framework                           |
|---------------------------------------------------------------|------------------------------------------------------------------------|---------------------------------------------------|
| AttributeUsageAttribute (Windows.Foundation.Metadata)         | AttributeUsageAttribute (System)                                       | System.Runtime.dll                                |
| AttributeTargets (Windows.Foundation.Metadata)                | AttributeTargets (System)                                              | System.Runtime.dll                                |
| DateTime (Windows.Foundation)                                 | DateTimeOffset (System)                                                | System.Runtime.dll                                |
| EventHandler&lt;T&gt; (Windows.Foundation)                    | EventHandler&lt;T&gt; (System)                                         | System.Runtime.dll                                |
| EventRegistrationToken (Windows.Foundation)                   | EventRegistrationToken (System.Runtime.InteropServices.WindowsRuntime) | System.Runtime.InteropServices.WindowsRuntime.dll |
| HResult (Windows.Foundation)                                  | Exception (System)                                                     | System.Runtime.dll                                |
| IReference&lt;T&gt; (Windows.Foundation)                      | Nullable&lt;T&gt; (System)                                             | System.Runtime.dll                                |
| TimeSpan (Windows.Foundation)                                 | TimeSpan (System)                                                      | System.Runtime.dll                                |
| Uri (Windows.Foundation)                                      | Uri (System)                                                           | System.Runtime.dll                                |
| IClosable (Windows.Foundation)                                | IDisposable (System)                                                   | System.Runtime.dll                                |
| IIterable&lt;T&gt; (Windows.Foundation.Collections)           | IEnumerable&lt;T&gt; (System.Collections.Generic)                      | System.Runtime.dll                                |
| IVector&lt;T&gt; (Windows.Foundation.Collections)             | IList&lt;T&gt; (System.Collections.Generic)                            | System.Runtime.dll                                |
| IVectorView&lt;T&gt; (Windows.Foundation.Collections)         | IReadOnlyList&lt;T&gt; (System.Collections.Generic)                    | System.Runtime.dll                                |
| IMap&lt;K,V&gt; (Windows.Foundation.Collections)              | IDictionary&lt;TKey,TValue&gt; (System.Collections.Generic)            | System.Runtime.dll                                |
| IMapView&lt;K,V&gt; (Windows.Foundation.Collections)          | IReadOnlyDictionary&lt;TKey,TValue&gt; (System.Collections.Generic)    | System.Runtime.dll                                |
| IKeyValuePair&lt;K,V&gt; (Windows.Foundation.Collections)     | KeyValuePair&lt;TKey,TValue&gt; (System.Collections.Generic)           | System.Runtime.dll                                |
| IBindableIterable (Windows.UI.Xaml.Interop)                   | IEnumerable (System.Collections)                                       | System.Runtime.dll                                |
| IBindableVector (Windows.UI.Xaml.Interop)                     | IList (System.Collections)                                             | System.Runtime.dll                                |
| INotifyCollectionChanged (Windows.UI.Xaml.Interop)            | INotifyCollectionChanged (System.Collections.Specialized)              | System.ObjectModel.dll                            |
| NotifyCollectionChangedEventHandler (Windows.UI.Xaml.Interop) | NotifyCollectionChangedEventHandler (System.Collections.Specialized)   | System.ObjectModel.dll                            |
| NotifyCollectionChangedEventArgs (Windows.UI.Xaml.Interop)    | NotifyCollectionChangedEventArgs (System.Collections.Specialized)      | System.ObjectModel.dll                            |
| NotifyCollectionChangedAction (Windows.UI.Xaml.Interop)       | NotifyCollectionChangedAction (System.Collections.Specialized)         | System.ObjectModel.dll                            |
| INotifyPropertyChanged (Windows.UI.Xaml.Data)                 | INotifyPropertyChanged (System.ComponentModel)                         | System.ObjectModel.dll                            |
| PropertyChangedEventHandler (Windows.UI.Xaml.Data)            | PropertyChangedEventHandler (System.ComponentModel)                    | System.ObjectModel.dll                            |
| PropertyChangedEventArgs (Windows.UI.Xaml.Data)               | PropertyChangedEventArgs (System.ComponentModel)                       | System.ObjectModel.dll                            |
| TypeName (Windows.UI.Xaml.Interop)                            | Type (System)                                                          | System.Runtime.dll                                |

 

Таблица 2. UWP типы, сопоставленные с типами .NET Framework с тем же именем и пространством имен.

| Пространство имен                           | Тип               | Сборка .NET Framework                   |
|-------------------------------------|--------------------|-------------------------------------------|
| Windows.UI                          | Color              | System.Runtime.WindowsRuntime.dll         |
| Windows.Foundation                  | отчетов              | System.Runtime.WindowsRuntime.dll         |
| Windows.Foundation                  | Rect               | System.Runtime.WindowsRuntime.dll         |
| Windows.Foundation                  | Размер               | System.Runtime.WindowsRuntime.dll         |
| Windows.UI.Xaml.Input               | ICommand           | System.ObjectModel.dll                    |
| Windows.UI.Xaml                     | CornerRadius       | System.Runtime.WindowsRuntime.UI.Xaml.dll |
| Windows.UI.Xaml                     | Продолжительность           | System.Runtime.WindowsRuntime.UI.Xaml.dll |
| Windows.UI.Xaml                     | DurationType       | System.Runtime.WindowsRuntime.UI.Xaml.dll |
| Windows.UI.Xaml                     | GridLength         | System.Runtime.WindowsRuntime.UI.Xaml.dll |
| Windows.UI.Xaml                     | GridUnitType       | System.Runtime.WindowsRuntime.UI.Xaml.dll |
| Windows.UI.Xaml                     | Thickness          | System.Runtime.WindowsRuntime.UI.Xaml.dll |
| Windows.UI.Xaml.Controls.Primitives | GeneratorPosition  | System.Runtime.WindowsRuntime.UI.Xaml.dll |
| Windows.UI.Xaml.Media               | Matrix (Матрица)             | System.Runtime.WindowsRuntime.UI.Xaml.dll |
| Windows.UI.Xaml.Media.Animation     | KeyTime            | System.Runtime.WindowsRuntime.UI.Xaml.dll |
| Windows.UI.Xaml.Media.Animation     | RepeatBehavior     | System.Runtime.WindowsRuntime.UI.Xaml.dll |
| Windows.UI.Xaml.Media.Animation     | RepeatBehaviorType | System.Runtime.WindowsRuntime.UI.Xaml.dll |
| Windows.UI.Xaml.Media.Media3D       | Matrix3D           | System.Runtime.WindowsRuntime.UI.Xaml.dll |

 

## <a name="related-topics"></a>См. также

* [Создание компонентов среды выполнения Windows в C# и Visual Basic](creating-windows-runtime-components-in-csharp-and-visual-basic.md)
