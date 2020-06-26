---
description: Записи и свойства таблицы стилей карты
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: Справочник по таблицам стилей карты
ms.date: 03/19/2017
ms.topic: article
keywords: windows 10, uwp, карты, таблица стилей карты
ms.localizationpriority: medium
ms.openlocfilehash: ec30fd5d63dfa522ef721d2d8a2e4950e6e8e854
ms.sourcegitcommit: c9edb164356c0843d8a9b19ab43707d486e392e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85377935"
---
# <a name="map-style-sheet-reference"></a>Справочник по таблицам стилей карты

Технологии сопоставления Майкрософт используют [таблицы стилей карты](https://docs.microsoft.com/BingMaps/styling/map-style-sheets) для определения внешнего вида карт, включая те, которые отображаются в [MapControl](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol)приложения Магазина Windows.  Таблица стилей карт определяется с помощью нотация объектов JavaScript (JSON) с помощью метода [мапстилешит. парсефромжсон](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapstylesheet.parsefromjson#Windows_UI_Xaml_Controls_Maps_MapStyleSheet_ParseFromJson_System_String_) .

Таблица стилей состоит из [записей](https://docs.microsoft.com/BingMaps/styling/map-style-sheet-entries) , свойства которых переопределяются для изменения окончательного внешнего вида схемы.

Например, следующий код JSON можно использовать для того, чтобы области воды отображались красным цветом, метки воды отображаются зелеными, а земли — синим.

```json
    {"version":"1.*",
        "settings":{"landColor":"#0000FF"},
        "elements":{"water":{"fillColor":"#FF0000","labelColor":"#00FF00"}}
    }
```

Таблицы стилей можно создавать в интерактивном режиме с помощью приложения [редактора таблицы стилей карт](https://www.microsoft.com/p/map-style-sheet-editor/9nbhtcjt72ft) .
