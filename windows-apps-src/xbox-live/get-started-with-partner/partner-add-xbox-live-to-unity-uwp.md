---
title: Unity для универсальной платформы Windows с помощью сценариев .NET
description: Добавить поддержку Xbox Live Unity для универсальной платформы Windows с помощью сценариев серверной части .NET для ID@Xbox и управляемых партнеров
ms.assetid: 790a49ad-eff4-4916-8578-968ca8483211
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10 для настольных ПК, xbox, один Unity
ms.localizationpriority: medium
ms.openlocfilehash: 8c4ca9d58f89e215563adcc7985b978641efdf07
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57594089"
---
# <a name="add-xbox-live-support-to-unity-for-uwp-with-net-scripting-backend-for-idxbox-and-managed-partners"></a>Добавить поддержку Xbox Live Unity для универсальной платформы Windows с помощью сценариев серверной части .NET для ID@Xbox и управляемых партнеров

**(1) установка Unity**

Установите Unity 5.3 или более поздней версии и во время Unity процесс установки, проверьте компонент «Серверная часть .NET сценарии Windows Store».

![](../images/unity/unity1-install.png)

**(2) откройте новый или существующий проект Unity**

Это может быть проект 3D или 2D. Любой из этих типов будет работать с пакетом SDK для Xbox Live.

**(3) Импорт последней версии пакета средств Unity-WinRT Xbox Live, его можно найти в https://github.com/Microsoft/xbox-live-api/releases**

**(4) добавьте и присоединить новый C\# сценарий для объектов Unity.**

Например, щелкните объект Unity, например «Main Camera» и нажмите кнопку «Добавить компонент» \| «Новый сценарий» \| C\# скрипт \| и назовите его «XboxLiveScript». Подойдет любой игровому объекту.

**(5) выполните сборку проекта в Unity.**

1.  Перейдите к файлу \| сборки настройки, убедитесь в том, щелкните «Изменить платформу», а затем Windows Store

2.  Нажмите кнопку «Добавить откройте сцены», добавляемый в текущую сцену сборки

3.  В поле со списком SDK выберите «Универсальной 10»

4.  В поле со списком типа сборки универсальной платформы Windows выбрать «D3D», но «XAML» также будет работать, если вы предпочитаете.

5.  Нажмите кнопку «Unity C\# проектов» флажок для создания проектов Csharp.dll сборки

6.  Нажмите кнопку «Построение» для Unity создать проект UWP в Visual Studio, который заключает в оболочку игры Unity в приложении универсальной платформы Windows. Когда получите запрос на указание расположения, создайте новую папку, чтобы избежать путаницы, так как будет создаваться большое количество новых файлов. Рекомендуется, можно вызвать папке «Сборки», а затем выберите эту папку

![](../images/unity/unity3-buildsettings.png)


**(6) откройте созданный проект UWP в Visual Studio**

Unity откроется в выходную папку проекта в обозревателе.  Игнорируйте SLN-файл существует.  Вместо этого перейдите в папку "сборки" и откройте созданный .sln в Visual Studio.  

Вы увидите 3 проекта в этом решении.

1.  Сборка CSharp. Это, где будет располагаться сценарии Xbox Live

2.  Сборка Csharp-firstpass. Этот проект можно пропустить для наших целей.

3.  Приложения универсальной платформы Windows, на основе имени проекта. Это традиционные приложения UWP, на котором размещается ядро Unity. Это происходит, когда будет настраиваться некоторые конфигурации, Xbox Live аналогичную традиционные приложения универсальной платформы Windows.


**(7) Добавление конфигурации Xbox Live в приложение универсальной платформы Windows**

Выполните на страницу документа под названием [Добавление Xbox Live в новый или существующий проект UWP](get-started-with-visual-studio-and-uwp.md)

**(8) добавьте код, Xbox Live в скрипт**

Скопируйте и вставьте этот пример кода, Xbox Live в сценарий, который вы подключили к игровому объекту. Этот сценарий будет отображаться в проекте «Сборка-CSharp». Вы можете изменить код, при необходимости.

```csharp
#if NETFX_CORE

using UnityEngine;
using System;
using Microsoft.Xbox.Services.System;

public class XboxLiveScript : MonoBehaviour
{
    Microsoft.Xbox.Services.System.XboxLiveUser m_user = new Microsoft.Xbox.Services.System.XboxLiveUser();
    Microsoft.Xbox.Services.XboxLiveContext m_xboxLiveContext = null;
    Windows.UI.Core.CoreDispatcher UIDispatcher = null;
    string debugText = "";

    void Start()
    {
        Windows.ApplicationModel.Core.CoreApplicationView mainView = Windows.ApplicationModel.Core.CoreApplication.MainView;
        Windows.UI.Core.CoreWindow cw = mainView.CoreWindow;

        UIDispatcher = cw.Dispatcher;
        SignIn();
    }

    void Update()
    {
    }

    void OnGUI()
    {
        GUI.Label(new UnityEngine.Rect(10, 10, 300, 50), debugText);
    }

    async void SignIn()
    {
        SignInResult result = await m_user.SignInAsync(UIDispatcher);

        if (result.Status == SignInStatus.Success)
        {
            m_xboxLiveContext = new Microsoft.Xbox.Services.XboxLiveContext(m_user);
            debugText += "\n User signed in: " + m_xboxLiveContext.User.Gamertag;
        }
    }
}

#endif
```

**9) Скомпилируйте и запустите приложение универсальной платформы Windows из Visual Studio**

Это запустит приложение как обычное приложение универсальной платформы Windows и разрешает вызовы, Xbox Live, работают так, как они требуют контейнер приложения универсальной платформы Windows для функции.

**10) перестроение при внесении изменений в Unity**  
При изменении любого элемента в Unity, вам необходимо перестроить проект универсальной платформы Windows.

Обратите внимание на то, что Unity заменит PFX-файла, при перекомпиляции, что вызовет Xbox Live входа переход на другой, поэтому необходимо обновить его внутри проекта Unity, чтобы избежать этой проблемы.

Чтобы сделать это, перейдите к файлу \| параметры построения щелкните «Параметры построения» на проигрыватель Windows Store и нажмите кнопку PFX-ФАЙЛ, чтобы заменить PFX-файла, полученный выше. Кроме того, можно удалить PFX-файла каждой повторной сборке проекта в Unity.

## <a name="troubleshooting-common-issues"></a>Устранение распространенных проблем

**1)** Unity, если есть что связанного скрипта можно не загружаются, а затем убедитесь, что вы уже делали шаге 3, чтобы перетащить WinMD панели активов проекта Unity

**2)** Если приложение аварийно завершает работу обеспечивал при запуске или при попытке запуска эта строка кода:

    Microsoft.Xbox.Services.System.XboxLiveUser m_user = new Microsoft.Xbox.Services.System.XboxLiveUser();

Убедитесь, что вы добавили xboxservices.config текстовый файл в проект и его свойств, набор «Действие сборки» в «Content» и «Копировать в выходной каталог» set «Всегда Копировать».

> [!NOTE]
> Все значения внутри xboxservices.config чувствительны к регистру.

Также убедитесь, что он содержит правильное JSON форматирование с TitleId в десятичной форме, например:

```json
{
    "TitleId" : 928643728,
    "PrimaryServiceConfigId" : "3ebd0100-ace5-4aa4-ab9c-5b733759fa90"
}
```

**3)** Если приложение запускается, но не сможет входить проверьте следующее:

) имеет значение компьютере изолированную среду разработчика.  Для этого можно используете сценарий SwitchSandbox.cmd в папке \Tools Xbox Live SDK.

(б) вы выполняете вход с учетной записи Xbox Live, имеющей доступ к песочнице разработчика.  Учетные записи Xbox Live обычный розничной торговли — нет доступа.  XDP или центра партнеров можно использовать для создания тестовых учетных записей.

) правильный идентификатор имеет значение вашим package.appxmanfiest в приложении универсальной платформы Windows.  Самый простой способ устранить эту проблему, — щелкнуть правой кнопкой мыши проект в Visual Studio и выберите «Store», но это можно изменить вручную \| «Связать приложение с Store».

(г) акций PFX-файл, предоставляемым Unity не имеют правильный идентификатор, поэтому либо удалите его с диска и удалить строку в CSPROJ-файле, ссылающаяся на нее, или щелкните проект в Visual Studio правой кнопкой и выберите «Store» \| «связать приложение с Store «что разместит вниз в правильной PFX-файл.  Не забудьте затем возвращаться в Unity, щелкните «Параметры построения» на проигрыватель Windows Store и нажмите кнопку PFX-ФАЙЛ, чтобы заменить PFX-файл, полученный из действия «Связать приложение с Store» Visual Studio.
