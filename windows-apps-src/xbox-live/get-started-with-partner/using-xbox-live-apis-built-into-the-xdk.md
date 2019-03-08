---
title: С помощью API-интерфейсы Live Xbox, встроенных в XDK
description: Узнайте, как использовать встроенные API Xbox Live в своем проекте Xbox Developer Kit (XDK).
ms.assetid: 539caca3-58bc-49d9-8432-ca8e57755be2
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: c762dd8abbbc80948d232610e4123b6e4893936d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57598409"
---
# <a name="using-xbox-live-apis-built-into-the-xdk"></a>С помощью API-интерфейсы Live Xbox, встроенных в XDK

1. Щелкните правой кнопкой проект в Visual Studio и выберите «Ссылки».
1. Кнопка «Добавить новую ссылку»
1. Щелкните «Durango. <build number>" и «Расширения» на левой панели
1. В середине выберите:
- Если вы хотите использовать WinRT XSAPI API, выберите «API службы Xbox»
- Если вы хотите использовать C++ XSAPI API, выберите «Cpp API службы Xbox»
1. Нажмите кнопку "ОК".

Примечание. Если система сборки не поддерживает файлы props, необходимо вручную добавить определения препроцессора и библиотек, как показано на `%XboxOneExtensionSDKLatest%\ExtensionSDKs\Xbox.Services.API.Cpp\8.0\DesignTime\CommonConfiguration\Neutral\Xbox.Services.API.Cpp.props`
