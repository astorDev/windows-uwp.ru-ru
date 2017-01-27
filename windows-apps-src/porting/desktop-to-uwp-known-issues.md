---
author: awkoren
Description: "В этой статье описываются известные проблемы, связанные с использованием Desktop Bridge для преобразования классических приложений в приложения UWP."
Search.Product: eADQiWindows 10XVcnh
title: "Известные проблемы с Desktop Bridge"
translationtype: Human Translation
ms.sourcegitcommit: ec4c5f937e4fd133bfc4f7aa96d00cee03a13c26
ms.openlocfilehash: d3ed0af32c9a44078d0f772d7fc130121f5d4970

---
# <a name="known-issues-with-the-desktop-bridge"></a>Известные проблемы с Desktop Bridge

В этой статье описываются известные проблемы, связанные с использованием Desktop Bridge для преобразования классических приложений в приложения UWP.

## <a name="blue-screen-with-error-code-0x139-kernelsecuritycheckfailure"></a>Синий экран с кодом ошибки 0x139 (KERNEL_SECURITY_CHECK_FAILURE)

После установки или запуска некоторых приложений из Магазина Windows компьютер может неожиданно перезагрузиться с ошибкой **0x139 (KERNEL\_SECURITY\_CHECK\_ FAILURE)**.

Известно, что эта проблема затрагивает приложения Kodi, JT2Go, Ear Trumpet, Teslagrad и др.

27.10.2016 г. было выпущено [обновление Windows (версия 14393.351 — KB3197954)](https://support.microsoft.com/kb/3197954), в которое вошли важные исправления, направленные на решение этой проблемы. Если вы столкнулись с этой проблемой, обновите свой компьютер. Если вам не удается обновить компьютер, потому что он перезапускается до того, как вам удается войти в систему, восстановите систему до точки более ранней, чем установка одного из затрагиваемых проблемой приложений. О том, как восстановить систему, см. в статье [Параметры восстановления в Windows 10](https://support.microsoft.com/en-us/help/12415/windows-10-recovery-options). 

Если обновление не устраняет проблему или вы не знаете, как восстановить компьютер, обратитесь в [службу поддержки Майкрософт](https://support.microsoft.com/contactus/). 

Если вы разработчик, вы можете запретить установку своих преобразованных с помощью Desktop Bridge приложений в версиях Windows, в которых нет этого обновления. Обратите внимание, что при этом ваше приложение не будет доступно пользователям, которые еще не установили обновление. Чтобы сделать приложением доступным только пользователям, которые установили обновления, внесите в файл AppxManifest.xml следующие исправления:

```<TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.14393.351" MaxVersionTested="10.0.14393.351"/>```

Подробнее о Центре обновления Windows можно прочитать по следующим ссылкам: 
* https://support.microsoft.com/kb/3197954
* https://support.microsoft.com/help/12387/windows-10-update-history


<!--HONumber=Dec16_HO3-->


