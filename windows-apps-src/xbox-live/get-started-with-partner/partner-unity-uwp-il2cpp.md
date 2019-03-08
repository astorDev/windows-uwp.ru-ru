---
title: Unity для UWP с серверной частью IL2CPP
description: Добавление поддержки Xbox Live в Unity для универсальной платформы Windows с IL2CPP сценариев серверной части для ID@Xbox и управляемых партнеров
ms.assetid: 790a49ad-eff4-4916-8578-968ca8483211
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10 для настольных ПК, xbox, один Unity
ms.localizationpriority: medium
ms.openlocfilehash: ace950dec6a57a9550ea7b3fbe6c52b53855e2e0
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57622919"
---
# <a name="add-xbox-live-support-to-unity-for-uwp-with-il2cpp-scripting-backend-for-idxbox-and-managed-partners"></a>Добавление поддержки Xbox Live в Unity для универсальной платформы Windows с IL2CPP сценариев серверной части для ID@Xbox и управляемых партнеров

## <a name="overview"></a>Обзор

Поддержка среды выполнения Windows для IL2CPP в Unity

В выпуске Unity 5.6f3 модуль включает в себя новый компонент, который позволяет разработчикам использовать компоненты среды выполнения Windows (WinRT) непосредственно в скрипте, включая их в игровой проект напрямую. Пока 5.6 разработчиков имеется необходимое подключаемого модуля или библиотеки dll для поддержки любого компонента платформы (включая Xbox Live SDK) из сценария игры в универсальной платформы Windows. Этот новый уровень проекции устраняет необходимость подключаемого модуля и предлагает новые и упрощенные рабочий процесс поддерживается только для игр, выберите IL2CPP сценариев серверной части.

Дополнительные сведения о том, как приступить к работе см. в документации Unity: https://docs.unity3d.com/Manual/IL2CPP-WindowsRuntimeSupport.html

## <a name="steps"></a>Действия

**(1) установка Unity**

Установить Unity 5.6 или более поздней версии и убедитесь в наличии **сценариев серверной части Windows Store Il2CPP** выбрать во время установки

**(2) установка средств Visual Studio для Unity версии 3.1 и выше для IntelliSense поддерживает при использовании файлы Winmd** для Visual Studio 2015, его можно найти в https://marketplace.visualstudio.com/items?itemName=SebastienLebreton.VisualStudio2015ToolsforUnity.  Для Visual Studio 2017 можно добавить компонент в установщике Visual Studio 2017.

**(3) откройте новый или существующий проект Unity**

**(4) переключитесь в платформе универсальной платформы Windows в меню "Параметры сборки Unity"**

**(5) включить IL2CPP сценариев серверной части в параметры проигрывателя Unity и значение .NET 4.6 совместимость API**

![](../images/unity/unity-il2cpp-1.png)

**(6) Импорт последней версии пакета средств Unity-WinRT Xbox Live** его можно найти в https://github.com/Microsoft/xbox-live-api/releases

**(7) Добавление и присоединение нового C\# сценарий для объектов Unity.**

Например, щелкните объект Unity, например «Main Camera» и нажмите кнопку «Добавить компонент» \| «Новый сценарий» \| C\# скрипт \| и назовите его «XboxLiveScript». Подойдет любой игровому объекту.

**(8) откройте скрипт в Visual Studio (с VSTU установлена 3.1 +)**

Вы заметите два проекта, откройте сценарий игры XboxLiveTest.cs проекта «Player», созданных в VSTU

![](../images/unity/unity-il2cpp-2.png)

Это представляет собой специальный проект, созданный для универсальной платформы Windows и содержит ссылки на файлы winmd, помещенные в ресурсах.
Он также будет определять «#if ENABLE_WINMD_SUPPORT» определить автоматически, поэтому IntelliSense и выделение синтаксиса будет работать должным образом.

**9) добавьте следующий код Xbox Live в исходный файл XboxLiveTest.cs**

```csharp

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using System;
public class XboxLiveTest : MonoBehaviour
{
#if ENABLE_WINMD_SUPPORT
    Microsoft.Xbox.Services.System.XboxLiveUser m_user = new   Microsoft.Xbox.Services.System.XboxLiveUser();

    Microsoft.Xbox.Services.XboxLiveContext m_xboxLiveContext = null;
    Windows.UI.Core.CoreDispatcher UIDispatcher = null;
#endif
    string debugText = "";
    // Use this for initialization
    void Start()
    {
#if ENABLE_WINMD_SUPPORT
        Windows.ApplicationModel.Core.CoreApplicationView mainView = Windows.ApplicationModel.Core.CoreApplication.MainView;
        Windows.UI.Core.CoreWindow cw = mainView.CoreWindow;
        UIDispatcher = cw.Dispatcher;
        SignIn();
#endif
    }
    // Update is called once per frame
    void Update()
    {
    }
    void OnGUI()
    {
        GUI.Label(new UnityEngine.Rect(10, 10, 300, 50), debugText);
    }
#if ENABLE_WINMD_SUPPORT
    async void SignIn()
    {
        Microsoft.Xbox.Services.System.SignInResult result = await m_user.SignInAsync(UIDispatcher);
        if (result.Status == Microsoft.Xbox.Services.System.SignInStatus.Success)
        {
            m_xboxLiveContext = new Microsoft.Xbox.Services.XboxLiveContext(m_user);
            debugText += "\n User signed in: " + m_xboxLiveContext.User.Gamertag;
        }

    }
#endif
}

```

**(10) убедитесь, что у вас есть возможность «InternetClient», выбранной в параметрах публикации, найденные в параметрах проигрывателя**

![](../images/unity/unity-il2cpp-3.png)

**(11) выполните сборку проекта в Unity.**

1.  Перейдите к файлу \| щелкните Параметры построения **универсальной платформы Windows** и убедитесь, что при нажатии кнопки **изменить платформу**

2.  Нажмите кнопку «Добавить откройте сцены», добавляемый в текущую сцену сборки

3.  В поле со списком SDK выберите «Универсальной 10»

4.  В поле со списком типа сборки универсальной платформы Windows выбрать «D3D», но «XAML» также будет работать, если вы предпочитаете.

5.  Нажмите кнопку «Построение» для Unity создать проект UWP в Visual Studio, который заключает в оболочку игры Unity в приложении универсальной платформы Windows. Когда получите запрос на указание расположения, создайте новую папку, чтобы избежать путаницы, так как будет создаваться большое количество новых файлов. Рекомендуется, можно вызвать папке «Сборки», а затем выберите эту папку

**12) добавьте конфигурацию Xbox Live в проект**

Добавьте в файл xboxservices.config:

![](../images/unity/unity-il2cpp-4.png)

Выполните на страницу документа под названием [Добавление Xbox Live в новый или существующий проект UWP](get-started-with-visual-studio-and-uwp.md)

> [!NOTE]
> Все значения внутри xboxservices.config чувствительны к регистру.

**13) Скомпилируйте и запустите приложение универсальной платформы Windows из Visual Studio**

Это запустит приложение как обычное приложение универсальной платформы Windows и разрешает вызовы, Xbox Live, работают так, как они требуют контейнер приложения универсальной платформы Windows для функции.

**14) перестроение при внесении изменений в Unity**  
При изменении любого элемента в Unity, вам необходимо перестроить проект универсальной платформы Windows.

Обратите внимание на то, что Unity заменит PFX-файла, при перекомпиляции, что вызовет Xbox Live входа переход на другой, поэтому необходимо обновить его внутри проекта Unity, чтобы избежать этой проблемы.

Чтобы сделать это, перейдите к файлу \| параметры сборки, щелкните «Параметры построения» на **универсальной платформы Windows** player и выберите файл PFX кнопку для замены PFX-ФАЙЛ с одним полученный выше. Кроме того, можно удалить PFX-файла каждой повторной сборке проекта в Unity.

## <a name="troubleshooting-common-issues"></a>Устранение распространенных проблем

**1)** Unity, если есть что связанного скрипта можно не загружаются, а затем убедитесь, что вы уже делали шаге 3, чтобы перетащить WinMD панели активов проекта Unity

**2)** Если приложение аварийно завершает работу сразу же при запуске или при попытке запуска эта строка кода:

    Microsoft.Xbox.Services.System.XboxLiveUser m_user = new Microsoft.Xbox.Services.System.XboxLiveUser();

Убедитесь, что вы добавили xboxservices.config текстовый файл в проект и его свойств, набор «Действие сборки» в «Content» и «Копировать в выходной каталог» set «Всегда Копировать».
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

(г) акций PFX-файл, предоставляемым Unity не имеют правильный идентификатор, поэтому либо удалите его с диска и удалить строку в CSPROJ-файле, ссылающаяся на нее, или щелкните проект в Visual Studio правой кнопкой и выберите «Store» \| «связать приложение с Store «что разместит вниз в правильной PFX-файл.  Убедитесь, что, то чтобы вернуться к Unity, щелкните «Параметры построения» на **универсальной платформы Windows** проигрывателя и щелкните кнопку PFX-ФАЙЛ, чтобы заменить файл PFX-файл на тот, который получен из действия «Связать приложение с Store» Visual Studio.
