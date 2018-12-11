---
title: Функционирование эмуляции x86 и ARM32 в ARM
description: Обзор эмуляции приложений x86 в ARM.
ms.date: 02/15/2018
ms.topic: article
keywords: windows 10 s, всегда подключен, эмуляция x86 в ARM
ms.localizationpriority: medium
ms.openlocfilehash: 22b8d55fa2074d18ed3e5f3fe9fa3ab8161637be
ms.sourcegitcommit: 8921a9cc0dd3e5665345ae8eca7ab7aeb83ccc6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8890561"
---
# <a name="how-x86-emulation-works-on-arm"></a>Функционирование эмуляции x86 в ARM
Благодаря эмуляции приложений x86 богатая экосистема приложений Win32 доступна в ARM. Это обеспечивает для пользователей уникальную возможность работать с существующим приложением x86 win32, не внося никаких изменений. Приложение даже не "знает", что выполняется на ПК с Windows в ARM, пока не вызовет определенные API ([IsWoW64Process2](https://msdn.microsoft.com/en-us/library/windows/desktop/mt804318.aspx)).

Уровень [WOW64](https://msdn.microsoft.com/en-us/library/windows/desktop/aa384249(v=vs.85).aspx) в Windows 10 позволяет выполнять код x86 в Windows 10 версии ARM64. Эмуляция x86 работает, компилируя блоки инструкций x86 в инструкции ARM64, реализуя оптимизации для повышения эффективности. Служба кэширует эти переведенные блоки кода, чтобы уменьшить издержки на перевод инструкций и обеспечить возможность оптимизации при следующем запуске кода. Кэши создаются для каждого модуля, чтобы другие приложения могли использовать их при первом запуске. 

Дополнительные сведения об этих технологиях см. в видео на канале Channel9 [Windows 10 в ARM](https://channel9.msdn.com/Events/Build/2017/P4171). 