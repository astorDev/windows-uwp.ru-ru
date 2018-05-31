---
author: mijacobs
Description: TODO
title: Взаимодействие с помощью геймпада и пульта дистанционного управления
ms.assetid: 784a08dc-2736-4bd3-bea0-08da16b1bd47
label: Gamepad and remote interactions
template: detail.hbs
isNew: true
ms.author: mijacobs
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 3713d74edd93f437726c04dd68b604cb8a22da8f
ms.sourcegitcommit: f9a4854b6aecfda472fb3f8b4a2d3b271b327800
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
ms.locfileid: "1392433"
---
# <a name="gamepad-and-remote-control-interactions"></a>Взаимодействие с помощью геймпада и пульта дистанционного управления

Приложения универсальной платформы Windows (UWP) теперь поддерживают ввод с помощью геймпада и пульта дистанционного управления. Геймпады и пульты управления — это основные устройства ввода для Xbox и телевизоров. Приложения UWP следует оптимизировать для этих типов устройств ввода так же, как и для клавиатуры и мыши на компьютере или сенсорного ввода на телефоне или планшете. Проверка этих устройств ввода в приложении — самый важный этап его оптимизации для Xbox и телевизора.
Теперь вы можете подключить и использовать геймпад с приложениями UWP на компьютере, что упростит их проверку.

Для успешной работы приложения UWP при использовании геймпада или пульта управления нужно принять во внимание следующее.

* [Аппаратные кнопки](../devices/designing-for-tv.md#hardware-buttons)— геймпад и пульт управления предоставляют разные кнопки и конфигурации.

* [Навигация и взаимодействие с фокусом XY](../devices/designing-for-tv.md#xy-focus-navigation-and-interaction) — навигация с фокусом XY позволяет пользователю осуществлять навигацию по интерфейсу приложения.

* [Режим мыши](../devices/designing-for-tv.md#mouse-mode) — режим мыши позволяет приложению эмулировать использование мыши, если перемещения фокуса по оси X и Y недостаточно.
