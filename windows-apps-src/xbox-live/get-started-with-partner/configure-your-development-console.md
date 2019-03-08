---
title: Настройка консоли разработки Xbox
description: Узнайте, как настроить консоль разработки Xbox для поддержки разработки Xbox Live.
ms.assetid: f8fd1caa-b1e9-4882-a01f-8f17820dfb55
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 479be2401e0c54801645ad1c0d91b11b7ffb6869
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57649019"
---
# <a name="configure-your-xbox-development-console"></a>Настройка консоли разработки Xbox

Настройка консоли разработчика:
- Получить ваши идентификаторы
- Задать изолированную среду для вашего пакеты средств разработки
- Вход с учетной записью разработчика

## <a name="get-your-ids"></a>Получить ваши идентификаторы
Чтобы включить "песочницы" и службы Xbox Live, необходимо получить несколько идентификаторов, чтобы настроить пакет SDK и название. Это можно сделать с помощью тот же процесс.

Выполните [конфигурации службы Xbox Live](../xbox-live-service-configuration.md) получить ваши идентификаторы

## <a name="set-your-sandbox-on-your-development-kits"></a>Задать изолированную среду для вашего пакеты средств разработки
Вы не сможете загрузить пакет SDK без установки свой идентификатор "песочницы". Чтобы сделать это, можно использовать «Xbox один Manager», устанавливаемого на Компьютере, XDK, или можно открыть командную строку с XDK и использовать команду настройки (xbconfig.exe) следующим образом:

Проверьте текущий изолированную среду. Xbconfig sandboxid введите в командной строке.

Если это не соответствует ожиданиям, измените ваш ИД "песочницы". Введите xbconfig sandboxid =<your sandbox id> в командной строке.

Перезагрузите консоль с помощью перезагрузки (xbreboot.exe) в командной строке.

Убедитесь, что правильно сбрасывает изолированную среду. Xbconfig sandboxid введите в командной строке.

## <a name="sign-in-with-a-development-account"></a>Вход с учетной записью разработчика

Можно создать учетные записи разработки, используемые для входа в систему [портале разработчиков Xbox (XDP)](https://xdp.xboxlive.com/User/Contact/MyAccess?selectedMenu=devaccounts) или [центра партнеров](https://partner.microsoft.com/dashboard)
