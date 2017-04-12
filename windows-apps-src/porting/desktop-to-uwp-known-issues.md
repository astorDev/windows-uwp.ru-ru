---
author: normesta
Description: "В этой статье описываются известные проблемы, касающиеся моста переноса классических приложений на UWP."
Search.Product: eADQiWindows 10XVcnh
title: "Мост переноса классических приложений на UWP: известные проблемы"
ms.author: normesta
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 71f8ffcb-8a99-4214-ae83-2d4b718a750e
ms.openlocfilehash: 1923d7eb8d2d4956d68a10d3727251eee0398b06
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="desktop-to-uwp-bridge-known-issues"></a>Мост переноса классических приложений на UWP: известные проблемы

В этой статье описываются известные проблемы, касающиеся моста переноса классических приложений на UWP.

## <a name="blue-screen-with-error-code-0x139-kernelsecuritycheckfailure"></a>Синий экран с кодом ошибки 0x139 (KERNEL_SECURITY_CHECK_FAILURE)

После установки или запуска некоторых приложений из Магазина Windows компьютер может неожиданно перезагрузиться с ошибкой **0x139 (KERNEL\_SECURITY\_CHECK\_ FAILURE)**.

Известно, что эта проблема затрагивает приложения Kodi, JT2Go, Ear Trumpet, Teslagrad и др.

27.10.2016г. было выпущено [обновление Windows (версия 14393.351— KB3197954)](https://support.microsoft.com/kb/3197954), в которое вошли важные исправления, направленные на решение этой проблемы. Если вы столкнулись с этой проблемой, обновите свой компьютер. Если вам не удается обновить компьютер, потому что он перезапускается до того, как вам удается войти в систему, восстановите систему до точки более ранней, чем установка одного из затрагиваемых проблемой приложений. О том, как восстановить систему, см. в статье [Параметры восстановления в Windows 10](https://support.microsoft.com/help/12415/windows-10-recovery-options).

Если обновление не устраняет проблему или вы не знаете, как восстановить компьютер, обратитесь в [службу поддержки Майкрософт](https://support.microsoft.com/contactus/).

Если вы разработчик, вы можете запретить установку своих преобразованных с помощью Desktop Bridge приложений в версиях Windows, в которых нет этого обновления. Обратите внимание, что при этом ваше приложение не будет доступно пользователям, которые еще не установили обновление. Чтобы сделать приложением доступным только пользователям, которые установили обновления, внесите в файл AppxManifest.xml следующие исправления:

```<TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.14393.351" MaxVersionTested="10.0.14393.351"/>```

Подробнее о Центре обновления Windows можно прочитать по следующим ссылкам:
* https://support.microsoft.com/kb/3197954
* https://support.microsoft.com/help/12387/windows-10-update-history
