---
title: Устранение неполадок при установке Xbox Live на ПК Windows
description: Узнайте, как для устранения неполадок среды разработки Xbox Live на ПК Windows.
ms.assetid: 9cfebdcd-0c1c-4fc2-9457-e7e434b6374c
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10 для настольных ПК, xbox, один, устранение неполадок
ms.localizationpriority: medium
ms.openlocfilehash: c1f055a49fe34be35335e50dc8b1efbfb7b9b922
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57647739"
---
# <a name="troubleshooting-xbox-live-setup-on-windows-pc"></a>Устранение неполадок при установке Xbox Live на ПК Windows

На ПК с Windows 10 вы можете обеспечить ваш компьютер настроен правильно, выполнив следующие действия:

1. Измените пункты XDKS.1 "песочницы", где примеры предназначены для работы на машине.  Этого можно запустить этот скрипт:

        {*SDK source root*}\Tools\SwitchSandbox.cmd XDKS.1

1. Извлеките содержимое ZIP-файла, найден «SourcesAndSamples.zip» внутри пакета SDK.
1. Откройте образец решения:
    1. Для C++ API: {*корень пакета SDK для источника*} \Samples\Social\UWP\Cpp\Social.Cpp.140.sln
    1. Для WinRT API с помощью C#: {*корень пакета SDK для источника*} \Samples\Social\UWP\CSharp\Social.CSharp.140.sln
    1. Для WinRT API с использованием C + +/ CX: {*корень пакета SDK для источника*} \Samples\TitleStorage\UWP\CppCx\TitleStorageUniversal.sln
1. Измените целевую платформу сборки «Win32» или «x64».
1. Правой кнопкой мыши решение и все, что повторное построение.
1. Запустите приложение в отладчике.
1. Войдите, используя учетную запись для разработки, созданной на [портале разработчиков Xbox](https://xdp.xboxlive.com), или с учетной записью разработчика розничной торговли, авторизованные в [центра партнеров](https://partner.microsoft.com/dashboard).
1. Предоставьте приложению разрешение на доступ к вашим данным Xbox Live.
1. Убедитесь, что приложение может получить данные, и вы увидите тегом игрока.