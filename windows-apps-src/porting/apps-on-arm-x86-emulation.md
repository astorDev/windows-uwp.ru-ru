---
title: Функционирование эмуляции x86 и ARM32 в ARM
author: msatranjr
description: Обзор эмуляции приложений x86 в ARM.
ms.author: misatran
ms.date: 02/15/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10 s, всегда подключен, эмуляция x86 в ARM
ms.localizationpriority: medium
ms.openlocfilehash: d8304bb49b1eeeea8b0aa17f7548197a360cbcf8
ms.sourcegitcommit: 11edca90aaf7856c762e68903483079d30ad3877
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2018
ms.locfileid: "1595183"
---
# <a name="how-x86-emulation-works-on-arm"></a>Функционирование эмуляции x86 в ARM
Благодаря эмуляции приложений x86 богатая экосистема приложений Win32 доступна в ARM. Это обеспечивает для пользователей уникальную возможность работать с существующим приложением x86 win32, не внося никаких изменений. Приложение даже не "знает", что выполняется на ПК с Windows в ARM, пока не вызовет определенные API ([IsWoW64Process2](https://msdn.microsoft.com/en-us/library/windows/desktop/mt804318.aspx)).

Уровень [WOW64](https://msdn.microsoft.com/en-us/library/windows/desktop/aa384249(v=vs.85).aspx) в Windows 10 позволяет выполнять код x86 в Windows 10 версии ARM64. Эмуляция x86 работает, компилируя блоки инструкций x86 в инструкции ARM64, реализуя оптимизации для повышения эффективности. Служба кэширует эти переведенные блоки кода, чтобы уменьшить издержки на перевод инструкций и обеспечить возможность оптимизации при следующем запуске кода. Кэши создаются для каждого модуля, чтобы другие приложения могли использовать их при первом запуске. 

Дополнительные сведения об этих технологиях см. в видео на канале Channel9 [Windows 10 в ARM](https://channel9.msdn.com/Events/Build/2017/P4171). 