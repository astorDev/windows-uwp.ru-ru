---
author: Mtoepke
title: "Вопросы и ответы"
description: "Вопросы и ответы о UWP на консолях Xbox."
translationtype: Human Translation
ms.sourcegitcommit: 1ee75d73cc42455677bc2c0df08b41f33fc4f7b0
ms.openlocfilehash: a6d410e3a4873bb96fba46789b28185c392a7492

---

# <a name="frequently-asked-questions"></a>Вопросы и ответы

Что-то работает не так, как вы ожидали? Прочитайте вопросы и ответы на этой странице. Также изучите раздел [Известные проблемы](known-issues.md) и посетите форум [разработки универсальных приложений для Windows](https://social.msdn.microsoft.com/Forums/windowsapps/en-US/home?forum=wpdevelop). 

### <a name="why-are-my-games-and-apps-not-working"></a>Почему игры и приложения не работают?

Если игры и приложения не работают или если у вас нет доступа к Магазину или службам Live, возможно, вы работаете в режиме разработчика. Чтобы определить, так ли это, откройте главную страницу. Если вы видите большую плитку Dev Home в правой части экрана, вместо обычного содержимого Gold или Live, то вы в режиме разработчика. Если вы хотите играть в игры, откройте Dev Home и переключитесь в коммерческий режим с помощью кнопки **Выйти из режима разработчика**.

### <a name="why-cant-i-connect-to-my-xbox-one-using-visual-studio"></a>Почему не удается подключиться к Xbox One в Visual Studio?

Сначала убедитесь, что используется режиме разработчика, а не коммерческий режим. Вы не сможете подключиться к консоли Xbox One, если она находится в коммерческом режиме. Вы можете просто нажать кнопку **Главная** и найдите плитку Dev Home в правой части экрана. Если плитки нет, и вы видите содержимое Gold или Live, вы в коммерческом режиме. Вам необходимо запустить приложение Dev Mode Activation, чтобы перейти в режим разработчика.

> [!NOTE]
> Для развертывания приложения пользователь должен выполнить вход.

Дополнительные сведения см. в разделе [Устранение ошибок развертывания](#fixing-deployment-failures) далее на этой странице.

### <a name="how-do-i-switch-between-retail-mode-and-developer-mode"></a>Как переключаться между коммерческим режимом и режимом разработчика?

Следуйте инструкциям для [активации режима разработчика Xbox One](devkit-activation.md), чтобы больше узнать об этих состояниях.

### <a name="how-do-i-know-if-i-am-in-retail-mode-or-developer-mode"></a>Как определить текущий режим — коммерческий или разработчика?

Следуйте инструкциям для [активации режима разработчика Xbox One](devkit-activation.md), чтобы больше узнать об этих состояниях. 

Вы можете просто нажать кнопку **Главная** и посмотреть на правую часть экрана. Если вы находитесь в режиме разработчика, вы увидите плитку Dev Home в правой части экрана. Если вы в коммерческом режиме, вы увидите обычное содержимое Gold и Live.

### <a name="will-my-games-and-apps-still-work-if-i-activate-developer-mode"></a>Будут ли игры и приложения по-прежнему работать, если включить режим разработчика?

Да, вы можете переключиться из режима разработчика в коммерческий режим, чтобы играть в игры. Дополнительные сведения см. на странице [Активация режима разработчика Xbox One](devkit-activation.md). 

### <a name="will-i-lose-my-games-and-apps-or-saved-changes"></a>Потеряю ли я игры и приложения или сохраненные изменения?

Если вы захотите выйти из программы для разработчиков, вы не потеряете установленные игры и приложения. Кроме того, если вы были подключены к Интернету, когда играли в игры, все сохраненные данные игр будут размещены в облачном профиле учетной записи Live, поэтому вы их не потеряете.

### <a name="how-do-i-leave-the-developer-program"></a>Как прекратить участие в программе для разработчиков?

Сведения о выходе из программы для разработчиков см. в разделе [Деактивация режима разработчика Xbox One](devkit-deactivation.md).

### <a name="i-sold-my-xbox-one-and-left-it-in-developer-mode-how-do-i-deactivate-developer-mode"></a>Я продал консоль Xbox One и оставил ее в режиме разработчика. Как отключить режим разработчика?

Если у вас больше нет доступа к Xbox One, вы можете выключить режим разработчика в Центре разработки для Windows. Дополнительные сведения см. в разделе **Деактивация консоли с помощью Центра разработки для Windows** статьи [Деактивация режима разработчика Xbox One](devkit-deactivation.md#deactivate-your-console-using-windows-dev-center). 

### <a name="i-left-the-developer-program-using-windows-dev-center-but-im-in-still-developer-mode-what-do-i-do"></a>Я покинул программу для разработчиков с помощью Центра разработки для Windows, но режим разработчика по-прежнему включен. Что делать?

Запустите Dev Home и нажмите кнопку **Выйти из режима разработчика**. Это приведет к перезапуску консоли в коммерческом режиме. 

### <a name="can-i-publish-my-app"></a>Можно ли опубликовать приложение?

Вы можете [публиковать приложения](../publish/index.md) через Центр разработки, если у вас есть [учетная запись разработчика](https://developer.microsoft.com/store/register). Приложения UWP, созданные и протестированными на коммерческих консолях Xbox One, будут проходить такой же процесс проверки и публикации, как и приложения для Windows, с применением дополнительных проверок на соответствие современным стандартам Xbox One.

### <a name="can-i-publish-my-game"></a>Можно ли опубликовать игру?

Вы можете использовать UWP и Xbox One в режиме разработчика для построения и тестирования ваших игр на Xbox One. Для публикации игр UWP необходимо зарегистрироваться в [ID@XBOX](http://www.xbox.com/Developers/id). 
[ID@XBOX](http://www.xbox.com/Developers/id) предоставляет разработчикам полный доступ к API Xbox Live для игр, включая счет игры и достижения, а также возможность использования многопользовательского режима (режима для нескольких устройств), сохранения в облаке и работы со всеми функциями Xbox Live в Xbox One. 
[ID@XBOX](http://www.xbox.com/Developers/id) также предоставляет доступ к наборам разработки Xbox One для игр, которым необходимо использовать весь потенциал оборудования Xbox One.

### <a name="will-the-standard-game-engines-work"></a>Будут ли работать стандартные игровые модули?

Изучите страницу [Известные проблемы](known-issues.md) для этого выпуска.

### <a name="what-capabilities-and-system-resources-are-available-to-uwp-games-on-xbox-one"></a>Какие возможности и системные ресурсы доступны играм UWP на Xbox One? 

Информацию см. в разделе [Системные ресурсы для приложений UWP и игр для Xbox One](system-resource-allocation.md).

### <a name="if-i-create-a-directx-12-uwp-game-will-it-run-on-my-xbox-one-in-developer-mode"></a>Если я создаю игру UWP на основе DirectX 12, будет ли она запускаться на Xbox One в режиме разработчика?

Информацию см. в разделе [Системные ресурсы для приложений UWP и игр для Xbox One](system-resource-allocation.md).

### <a name="will-the-entire-uwp-api-surface-be-available-on-xbox"></a>Будет ли вся поверхность UWP API доступна на Xbox?

Изучите страницу [Известные проблемы](known-issues.md) для этого выпуска.

### <a name="fixing-deployment-failures"></a>Устранение ошибок развертывания

Если вам не удается развернуть приложение из Visual Studio, эти действия помогут вам устранить проблему. Если проблема сохраняется, посетите форум.

> [!NOTE]
> Для развертывания приложения пользователь должен выполнить вход. Если вы получили сообщение об ошибке 0x87e10008, убедитесь, что какой-либо пользователь выполнил вход, и повторите попытку.

Если Visual Studio не может подключиться к Xbox One:

1. Убедитесь, что вы используете режим разработчика (описано выше на этой странице).
2. Убедитесь, что вы настроили компьютер разработки правильно. Вы следовали *всем* инструкциям из раздела [Начало разработки приложений UWP на Xbox One](getting-started.md)? 

3. Если нет, прочитайте разделы [Настройка среды разработки](development-environment-setup.md) и [Вводные сведения об инструментах Xbox One](introduction-to-xbox-tools.md).

4. Убедитесь, что вы можете связаться с IP-адресом консоли с компьютера разработки.
  > [!NOTE]
  > Для повышения производительности развертывания рекомендуется использовать проводное подключение к консоли.

5. Убедитесь, что в раскрывающемся списке "Проверка подлинности" на вкладке **Отладка** выбран пункт "Универсальный (незашифрованный протокол)". Подробнее см. в разделе [Настройка среды разработки](development-environment-setup.md).

<!--6. Make sure you are not hitting a PIN pairing issue; see "Visual Studio/Xbox PIN pairing failures" in the [Known Issues](known-issues.md) topic.-->

<!--
If Visual Studio can connect, but deployment is failing (for example you get this error message: "DEP0700 : Registration of the app failed.(0x80073cf9)"):

1. Make sure that your app is not installed by uninstalling it from the Collections app in the Xbox One shell. 

> **Note**&nbsp;&nbsp;Uninstalling your app from Windows Device Portal (WDP) will not resolve the issue.

2. If your issues persist, uninstall your app or game in the Collections app, leave Developer Mode, restart to Retail Mode, and then switch back to Developer Mode. 
This will clear Dev Storage.

3. If your issues persist, follow the steps above and then use **Reset and keep my games & apps** to delete any stored state on your Xbox One. 
Go to Settings > System > Console info & updates > Reset console, and select the **Reset and keep my games & apps** button.

> **Caution**&nbsp;&nbsp;Doing this will delete all saved settings on your Xbox One including wireless settings, user accounts and any game progress that has not been saved to cloud storage.

> **Caution**&nbsp;&nbsp;DO NOT select the **Reset and remove everything** button.
This will delete all of your games, apps, settings and content and deactivate Developer Mode.
-->

### <a name="if-im-building-an-app-using-htmljavascript-how-do-i-enable-gamepad-navigation"></a>Если я создаю приложения с помощью HTML и JavaScript, как включить навигацию геймпада?

TVHelpers — это набор примеров и библиотек JavaScript и XAML/C#, которые помогут вам создавать замечательные приложения для Xbox One и телевизоров на языках JavaScript и C#. TVJS — это библиотека, которая позволяет создавать великолепные приложения UWP для Xbox One. TVJS включает поддержку автоматической навигации контроллера, богатые возможности воспроизведения мультимедиа, функцию поиска и многое другое. TVJS настолько же удобно использовать с размещенным веб-приложением, как и с упакованным веб-приложением UWP с полным доступом к API среды выполнения Windows.

Дополнительные сведения см. в разделах [TVHelpers](https://github.com/Microsoft/TVHelpers) и [Справка](https://github.com/Microsoft/TVHelpers/wiki).

## <a name="see-also"></a>См. также
- [Известные проблемы с UWP на Xbox One](known-issues.md)
- [Приложения UWP для Xbox One](index.md)



<!--HONumber=Dec16_HO1-->


