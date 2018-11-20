---
title: Функционирование эмуляции x86 и ARM32 в ARM
author: msatranjr
description: Обзор эмуляции приложений x86 в ARM.
ms.author: misatran
ms.date: 02/15/2018
ms.topic: article
keywords: windows 10 s, всегда подключен, эмуляция x86 в ARM
ms.localizationpriority: medium
ms.openlocfilehash: 6b596ab9abd31fa10d0ca07dec973082b495262e
ms.sourcegitcommit: cbe7cf620622a5e4df7414f9e38dfecec1cfca99
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "7446093"
---
# <a name="how-x86-emulation-works-on-arm"></a>Функционирование эмуляции x86 в ARM
Благодаря эмуляции приложений x86 богатая экосистема приложений Win32 доступна в ARM. Это обеспечивает для пользователей уникальную возможность работать с существующим приложением x86 win32, не внося никаких изменений. Приложение даже не "знает", что выполняется на ПК с Windows в ARM, пока не вызовет определенные API ([IsWoW64Process2](https://msdn.microsoft.com/en-us/library/windows/desktop/mt804318.aspx)).

Уровень [WOW64](https://msdn.microsoft.com/en-us/library/windows/desktop/aa384249(v=vs.85).aspx) в Windows 10 позволяет выполнять код x86 в Windows 10 версии ARM64. Эмуляция x86 работает, компилируя блоки инструкций x86 в инструкции ARM64, реализуя оптимизации для повышения эффективности. Служба кэширует эти переведенные блоки кода, чтобы уменьшить издержки на перевод инструкций и обеспечить возможность оптимизации при следующем запуске кода. Кэши создаются для каждого модуля, чтобы другие приложения могли использовать их при первом запуске. 

Дополнительные сведения об этих технологиях см. в видео на канале Channel9 [Windows 10 в ARM](https://channel9.msdn.com/Events/Build/2017/P4171). 