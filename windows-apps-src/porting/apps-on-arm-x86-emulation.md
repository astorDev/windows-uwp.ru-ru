---
title: Функционирование эмуляции x86 и ARM32 в ARM
description: Обзор эмуляции приложений x86 в ARM.
ms.date: 02/15/2018
ms.topic: article
keywords: windows 10 s, всегда подключен, эмуляция x86 в ARM
ms.localizationpriority: medium
ms.openlocfilehash: 8af6ba39468dd16a043040b797a03c862d6ce7db
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67319649"
---
# <a name="how-x86-emulation-works-on-arm"></a>Принцип работы эмуляции x86 на архитектуре ARM
Благодаря эмуляции приложений x86 богатая экосистема приложений Win32 доступна в ARM. Это обеспечивает для пользователей уникальную возможность работать с существующим приложением x86 win32, не внося никаких изменений. Приложение даже не "знает", что выполняется на ПК с Windows в ARM, пока не вызовет определенные API ([IsWoW64Process2](https://docs.microsoft.com/windows/desktop/api/wow64apiset/nf-wow64apiset-iswow64process2)).

Уровень [WOW64](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications) в Windows 10 позволяет выполнять код x86 в Windows 10 версии ARM64. Эмуляция x86 работает, компилируя блоки инструкций x86 в инструкции ARM64, реализуя оптимизации для повышения эффективности. Служба кэширует эти переведенные блоки кода, чтобы уменьшить издержки на перевод инструкций и обеспечить возможность оптимизации при следующем запуске кода. Кэши создаются для каждого модуля, чтобы другие приложения могли использовать их при первом запуске. 

Дополнительные сведения об этих технологиях см. в видео на канале Channel9 [Windows 10 в ARM](https://channel9.msdn.com/Events/Build/2017/P4171). 