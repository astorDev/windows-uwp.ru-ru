---
title: Unity для XDK с серверной частью IL2CPP
description: Добавление поддержки Xbox Live Unity для XDK IL2CPP сценариев серверной части для ID@Xbox и управляемых партнеров
ms.assetid: 790a49ad-eff4-4916-8578-968ca8483211
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10 для настольных ПК, xbox, один Unity
ms.localizationpriority: medium
ms.openlocfilehash: cfd722ca0d0b080f6395680cd62000cea9b402fa
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57608469"
---
# <a name="add-xbox-live-support-to-unity-for-xdk-with-il2cpp-scripting-backend-for-idxbox-and-managed-partners"></a>Добавление поддержки Xbox Live Unity для XDK IL2CPP сценариев серверной части для ID@Xbox и управляемых партнеров

## <a name="overview"></a>Обзор

Поддержка среды выполнения Windows для IL2CPP в Unity

В выпуске Unity 5.6f3 модуль включает в себя новый компонент, который позволяет разработчикам использовать компоненты среды выполнения Windows (WinRT) непосредственно в скрипте, включая их в игровой проект напрямую. Пока 5.6 разработчиков имеется необходимое подключаемого модуля или библиотеки dll для поддержки любого компонента платформы (включая Xbox Live SDK) из сценария игры. Этот новый уровень проекции устраняет необходимость подключаемого модуля и предлагает новые и упрощенные рабочий процесс поддерживается только для игр, выберите IL2CPP сценариев серверной части.

Дополнительные сведения о том, как приступить к работе см. в документации Unity: https://docs.unity3d.com/Manual/IL2CPP-WindowsRuntimeSupport.html

## <a name="steps"></a>Действия

**(1) установка Unity**

Установите Unity 5.6 или более поздней версии и убедитесь, что у вас есть Xbox One редактор расширение будет устанавливаться.

**(2) установка средств Visual Studio для Unity версии 3.1 и выше для IntelliSense поддерживает при использовании файлы Winmd** для Visual Studio 2015, его можно найти в https://marketplace.visualstudio.com/items?itemName=SebastienLebreton.VisualStudio2015ToolsforUnity.  Для Visual Studio 2017 можно добавить компонент в установщике Visual Studio 2017.

**(3) откройте новый или существующий проект Unity**

**(4) переключитесь в платформе Xbox One в меню "Параметры сборки Unity"**

**(5) включить IL2CPP сценариев серверной части в параметры проигрывателя Unity и значение .NET 4.6 совместимость API**

![](../images/unity/unity-il2cpp-1.png)

**(6) переключитесь в скрипт компилятора Roslyn**

**(7) Xbox One соответствующих системных библиотек все автоматически будет добавляться в проект и дополнительные шаги не требуются для включения двоичные файлы платформы.**

**8) Добавление и присоединить новый C\# сценарий для объектов Unity.**

Например, щелкните объект Unity, например «Main Camera» и нажмите кнопку «Добавить компонент» \| «Новый сценарий» \| C\# скрипт \| и назовите его «XboxLiveScript». Подойдет любой игровому объекту.

**9) откройте скрипт в Visual Studio (с VSTU установлена 3.1 +)**

Вы заметите два проекта, откройте сценарий игры XboxLiveTest.cs проекта «Player», созданных в VSTU

![](../images/unity/unity-il2cpp-2.png)

Это представляет собой специальный проект, созданный для XDK и включает в себя ссылки на файлы winmd, помещенные в ресурсах.
Он также будет определять «#if ENABLE_WINMD_SUPPORT» определить автоматически, поэтому IntelliSense и выделение синтаксиса будет работать должным образом.

**(10) добавьте следующий код Xbox Live в исходный файл XboxLiveTest.cs**

```csharp

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using System;

public class XboxLiveTest : MonoBehaviour
{
#if ENABLE_WINMD_SUPPORT
    Windows.Xbox.System.User mCurrentUser = null;
    XboxLiveContext mContext = null;
#endif

    // Use this for initialization
    void Start()
    {
#if ENABLE_WINMD_SUPPORT
        mCurrentUser = Windows.Xbox.ApplicationModel.Core.CoreApplicationContext.CurrentUser;
        mContext = new XboxLiveContext(mCurrentUser);
#endif
    }

    // Update is called once per frame
    void Update()
    {
    }

    void OnGUI()
    {
        if(mContext != null) Gui.TextArea(new Rect(10,10,50,200), mContext.XboxUserId);
    }
}

```

**(11) убедитесь, что у вас есть возможность «InternetClient», выбранной в параметрах публикации, найденные в параметрах проигрывателя**

![](../images/unity/unity-il2cpp-3.png)

**12) выполните сборку проекта в Unity.**
