---
title: Использование XIM (Unity с IL2CPP)
description: С помощью интегрированной многопользовательскую Xbox с помощью Unity для универсальной платформы Windows с помощью сценариев серверной части IL2CPP
ms.date: 04/03/2018
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10 для настольных ПК, xbox, один, Unity, встроенная многопользовательскую Xbox
ms.localizationpriority: medium
ms.openlocfilehash: a600fd253efae1daca34241b105a69514561e01d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57646449"
---
# <a name="use-xim-unity-with-il2cpp"></a>Использование XIM (Unity с IL2CPP)

## <a name="overview"></a>Обзор

Поддержка среды выполнения Windows для IL2CPP в Unity

В выпуске Unity 5.6f3 модуль включает в себя новый компонент, который позволяет разработчикам использовать компоненты среды выполнения Windows (WinRT) непосредственно в скрипте, включая их в игровой проект напрямую. Пока 5.6 разработчиков имеется необходимое подключаемого модуля или dll для поддержки любого компонента платформы (включая Xbox интегрированной многопользовательскую) из сценария игры в универсальной платформы Windows. Этот новый уровень проекции устраняет необходимость подключаемого модуля и предлагает новые и упрощенные рабочий процесс поддерживается только для игр, выберите IL2CPP сценариев серверной части.

- Дополнительные сведения о том, как приступить к работе с WinRT и Unity, см. в разделе [документации Unity](https://docs.unity3d.com/Manual/IL2CPP-WindowsRuntimeSupport.html).
- Дополнительные сведения о том, как добавить поддержку Xbox Live в Unity с помощью IL2CPP см. в разделе [Xbox Live документации](https://docs.microsoft.com/windows/uwp/xbox-live/get-started-with-partner/partner-add-xbox-live-to-unity-uwp) по этой теме.

## <a name="using-the-xim-unity-asset-package"></a>С помощью пакета средств XIM Unity

### <a name="1-install-unity"></a>1. Установка Unity

Установить Unity 5.6 или более поздней версии и убедитесь в наличии **сценариев серверной части Windows Store Il2CPP** выбрать во время установки

### <a name="2-install-visual-studio-tools-for-unity-version-31-and-above-for-intellisense-support-when-using-winmds"></a>2. Установка средств Visual Studio для Unity версии 3.1 и выше для IntelliSense поддерживает при использовании файлы Winmd

Для Visual Studio 2015, его можно найти [в Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SebastienLebreton.VisualStudio2015ToolsforUnity). Для Visual Studio 2017 можно добавить компонент в установщике Visual Studio 2017.

### <a name="3-open-a-new-or-existing-unity-project"></a>3. Откройте новый или существующий проект Unity

### <a name="4-switch-the-platform-to-universal-windows-platform-in-the-unity-build-settings-menu"></a>4. Переключиться в режим платформы универсальной платформы Windows в меню "Параметры сборки Unity"

![Меню параметров сборки Unity с помощью универсальной платформы Windows построить выбрали параметр](../../images/xboxintegratedmultiplayer/xim-unity-build.png)

### <a name="5-enable-il2cpp-scripting-backend-in-the-unity-player-settings-and-set-api-compatibility-to-net-46"></a>5. Включить IL2CPP сценариев серверной части в параметры проигрывателя Unity и значение .NET 4.6 совместимость API

![Разделе конфигурации из меню "Параметры проигрывателя Unity" параметр «Совместимость Api», значение «.NET 4.6"](../../images/unity/unity-il2cpp-1.png)

### <a name="6-import-the-latest-version-of-the-xbox-integrated-multiplayer-winrt-unity-asset-package"></a>6. Импорт последней версии пакета средств Xbox интегрированной многопользовательскую WinRT Unity

Его можно найти в https://github.com/Microsoft/xbox-integrated-multiplayer-unity-plugin/releases

### <a name="7-you-can-now-use-xim-in-your-scripts"></a>7. Теперь можно использовать XIM в скриптах

Дополнительные рекомендации по использованию XIM с C#, см. в разделе [использовать XIM (C#)](using-xim-cs.md).

В следующем фрагменте показано, как XIM могут интегрироваться с кодом:

```cs

using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

#if ENABLE_WINMD_SUPPORT
using Microsoft.Xbox.Services.XboxIntegratedMultiplayer;
#endif

public class XimScript
{
    public void Start()
    {
#if ENABLE_WINMD_SUPPORT
        XboxIntegratedMultiplayer.TitleId = XboxLiveAppConfiguration.SingletonInstance.TitleId;
        XboxIntegratedMultiplayer.ServiceConfigurationId = XboxLiveAppConfiguration.SingletonInstance.ServiceConfigurationId;
#endif
    }

    public void Update()
    {
#if ENABLE_WINMD_SUPPORT
        using (var stateChanges = XboxIntegratedMultiplayer.GetStateChanges())
        {
            foreach (var stateChange in stateChanges)
            {
                switch (stateChange.Type)
                {
                    case XimStateChangeType.PlayerJoined:
                        HandlePlayerJoined((XimPlayerJoinedStateChange)stateChange);
                        break;

                    case XimStateChangeType.PlayerLeft:
                        HandlePlayerLeft((XimPlayerLeftStateChange)stateChange);
                        break;
                    [...]
                }
            }
        }
#endif
    }
}
```

Дополнительные сведения о `ENABLE_WINMD_SUPPORT` #define директива, см. в документации Unity на [поддержка среды выполнения Windows](https://docs.unity3d.com/Manual/IL2CPP-WindowsRuntimeSupport.html)

### <a name="8-required-capability-content"></a>8. Требуемая возможность содержимого

Приложение, использующее XIM по своей природе требует подключение и принимать подключения из сетевых ресурсов, как через Интернет и локальную сеть. Также требуется доступ к устройствам микрофон для поддержки разговора. Таким образом приложения следует объявить «InternetClientServer» и «PrivateNetworkClientServer» возможности и возможность устройства «Микрофон» в параметрах публикации, найденные в параметрах проигрывателя.

![Unity по возможности меню с «InternetClientServer», «PrivateNetworkClientServer» и «Микрофон» выбранной возможности](../../images/xboxintegratedmultiplayer/xim-unity-capability.png)

### <a name="9-build-the-project-in-unity"></a>9. Постройте проект в Unity.

1. Перейдите к файлу \| щелкните Параметры построения **универсальной платформы Windows** и убедитесь, что при нажатии кнопки **изменить платформу**

2. Нажмите кнопку «Добавить откройте сцены», добавляемый в текущую сцену сборки

3. В поле со списком SDK выберите «Универсальной 10»

4. В поле со списком типа сборки универсальной платформы Windows выбрать «D3D», но «XAML» также будет работать, если вы предпочитаете.

5. Нажмите кнопку «Построение» для Unity создать проект UWP в Visual Studio, который заключает в оболочку игры Unity в приложении универсальной платформы Windows.

    Когда получите запрос на указание расположения, создайте новую папку, чтобы избежать путаницы, так как будет создаваться большое количество новых файлов. Рекомендуется, можно вызвать папке «Сборки», а затем выберите эту папку.

6. Добавьте в проект манифест XIM по сети

    Добавьте в файл networkmanifest.xml:

    ![Свойства networkmanifest.xml Visual Studio](../../images/xboxintegratedmultiplayer/xim-unity-networkmanifest.png)

    См. в разделе [конфигурации проекта XIM](xim-manifest.md) Дополнительные сведения о манифесте сети и его содержимого.

7. Скомпилируйте и запустите приложение UWP из Visual Studio

Это запустит приложение как обычное приложение универсальной платформы Windows и разрешает вызовы, Xbox Live, работают так, как они требуют контейнер приложения универсальной платформы Windows для функции.

### <a name="10-rebuild-if-you-make-changes-to-anything-in-unity"></a>10. Перестроить при внесении изменений в Unity

При изменении любого элемента в Unity, затем необходимо перестроить проект универсальной платформы Windows
