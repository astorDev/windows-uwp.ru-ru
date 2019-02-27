---
title: Руководство по разработке игр для Windows10
description: Комплексное руководство по ресурсам и сведения о разработке игр для универсальной платформы Windows (UWP).
ms.assetid: 6061F498-96A8-44EF-9711-68AE5A1218C9
ms.date: 04/16/2018
ms.topic: article
keywords: windows 10, uwp, игры, разработка игр
ms.localizationpriority: medium
ms.openlocfilehash: 38fc73eb602c1307fdd345d02c621791feb89dc2
ms.sourcegitcommit: ff131135248c85a8a2542fc55437099d549cfaa5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "9117744"
---
# <a name="windows-10-game-development-guide"></a>Руководство по разработке игр для Windows 10


Предлагаем вашему вниманию руководство по разработке игр для Windows 10.

Это руководство содержит полную коллекцию ресурсов и сведений, необходимых для разработки игры для универсальной платформы Windows (UWP). Версия этого руководства на английском языке (США) доступна в формате [PDF](https://download.microsoft.com/download/9/C/9/9C9D344F-611F-412E-BB01-259E5C76B17F/Windev_Game_Dev_Guide_Oct_2017.pdf).

## <a name="introduction-to-game-development-for-the-universal-windows-platform-uwp"></a>Введение в разработку игр для универсальной платформы Windows (UWP)


При создании игры для Windows10 вам предоставляется возможность выйти на многомиллионную аудиторию игроков по всему миру на телефонах, компьютерах и Xbox One. Благодаря службе Xbox в Windows, Xbox Live, возможности многопользовательской игры на разных устройствах, замечательному игровому сообществу и мощным новым функциям, таким как универсальная платформа Windows (UWP) и DirectX 12, игры для Windows10 самых разных жанров привлекают игроков всех возрастов. Новая универсальная платформа Windows (UWP) обеспечивает совместимость игры на разных устройствах Windows 10 с помощью общего API для телефонов, компьютеров и Xbox One, а также средств и возможностей для адаптации игры к возможностям каждого устройства.

Это руководство содержит полную коллекцию ресурсов и сведений, которые помогут вам в разработке игры. Разделы упорядочены по этапам разработки игры, поэтому вы будете знать, где находится нужная информация, когда она потребуется.

Если вы новичок в разработке игр для Windows или Xbox, имеет смысл начать с руководства [Начало работы](getting-started.md). В разделе [Ресурсы по разработке игр](#game-development-resources) представлен общий обзор документации, программ и других ресурсов, полезных при создании игр. Если же вы хотите начать с рассмотрения кода для UWP, обратитесь к разделу [Примеры игр](#game-samples).

Данное руководство будет обновляться по мере публикации дополнительных ресурсов и материалов по разработке игр для Windows 10.

## <a name="game-development-resources"></a>Ресурсы по разработке игр

Документация к программам для разработчиков, форумы, блоги и примеры — вам доступно множество ресурсов, помогающих в разработке игр. Здесь приведен обзор ресурсов, о которых следует знать, приступая к разработке игры для Windows 10.

> [!Note]
> Некоторые компоненты управляются в рамках различных программ. Это руководство описывает широкий спектр ресурсов, и некоторые ресурсы могут оказаться недоступны в зависимости от программы, участником которой вы являетесь, или от вашего статуса разработчика. В качестве примера можно назвать ссылки на ресурсы developer.xboxlive.com, forums.xboxlive.com, xdi.xboxlive.com или сеть разработчиков игр (GDN). Сведения о том, как стать партнером Microsoft, см. в разделе [Программы для разработчиков](#developer-programs).


### <a name="game-development-documentation"></a>Документация по разработке игр

В данном руководстве вы найдете прямые ссылки на соответствующую документацию, упорядоченные по задачам, технологиям и этапам разработки игр. Чтобы получить представление о доступных ресурсах, ознакомьтесь с перечнем основных порталов с документацией по разработке игр для Windows 10.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Главный портал Центра разработки для Windows</td>
        <td><a href="https://dev.windows.com">Центр разработки для Windows</a></td>
    </tr>
    <tr>
        <td>Разработка приложений для Windows</td>
        <td><a href="https://dev.windows.com/develop">Разработка приложений для Windows</a></td>
    </tr>
    <tr>
        <td>Разработка приложений универсальной платформы Windows</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt244352">Инструкции к приложениям для Windows 10</a></td>
    </tr>
    <tr>
        <td>Инструкции к играм для UWP</td>
        <td><a href="index.md">Игры и DirectX</a> </td>
    </tr>
    <tr>
        <td>Справочник и обзоры по DirectX</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/ee663274">Графика и игры на основе DirectX</a></td>
    </tr>
    <tr>
        <td>Azure для игр</td>
        <td><a href="https://azure.microsoft.com/solutions/gaming/">Создание и масштабирования игр с помощью Azure</a></td>
    </tr>
    <tr>
        <td>PlayFab</td>
        <td><a href="https://api.playfab.com/">Комплексное решение серверной части для игр реального времени</a></td>
    </tr>
    <tr>
        <td>UWP на Xbox One</td>
        <td><a href="https://msdn.microsoft.com/windows/uwp/xbox-apps/index">Создание приложений UWP для Xbox One</a></td>
    </tr>
    <tr>
        <td>UWP для HoloLens</td>
        <td><a href="https://developer.microsoft.com/windows/mixed-reality/development_overview">Создание приложений UWP для HoloLens</a></td>
    </tr>
    <tr>
        <td>Документация по Xbox Live</td>
        <td><a href="../xbox-live/index.md">Руководство по Xbox Live для разработчиков</a></td>
    </tr>
    <tr>
        <td>Документация для разработчиков по Xbox One (XGD)</td>
        <td><a href="https://developer.microsoft.com/games/xbox/partner/development-home">Разработка для Xbox One</a></td>
    </tr>
    <tr>
        <td>Технические документы для разработчиков по Xbox One (XGD)</td>
        <td><a href="https://developer.microsoft.com/games/xbox/partner/development-education-whitepapers">Технические документы</a></td>
    </tr>
    <tr>
        <td>Интерактивная документация по Mixer</td>
        <td><a href="https://dev.mixer.com/reference/interactive/index.html">Добавление интерактивных возможностей в игру</a></td>
    </tr>        
</table>

### <a name="partner-center"></a>Центр партнеров

[Регистрация учетной записи разработчика в центре партнеров](https://developer.microsoft.com/store/register) — это первый шаг к публикации игры для Windows. Учетная запись разработчика позволит вам резервировать названия для своих игр и отправлять в Microsoft Store бесплатные и платные игры для любых устройств с Windows. С помощью учетной записи разработчика вы сможете управлять своими играми и внутренними продуктами приложений, получать подробную аналитику и включать службы для предоставления замечательных возможностей вашим игрокам по всему миру. 

Корпорация Майкрософт также предлагает несколько программ для разработчиков, призванных помочь вам разрабатывать и публиковать игры для Windows. Рекомендуется проверить, подходят ли они вам перед регистрацией учетной записи центра партнеров. Подробнее см. в разделе [Программы для разработчиков](#developer-programs).


### <a name="developer-programs"></a>Программы для разработчиков

Корпорация Майкрософт предлагает несколько программ для разработчиков, призванных помочь вам разрабатывать и публиковать игры для Windows. Рассмотрите возможность присоединения к программе для разработчиков, если вы хотите создавать игры для Xbox One и использовать в них функции Xbox Live. Чтобы опубликовать игру в Microsoft Store, вам также понадобится создать учетную запись разработчика в [Центре партнеров](https://partner.microsoft.com/dashboard) .

#### <a name="xbox-live-creators-program"></a>Программа Xbox Live Creators Program

Программа Xbox Live Creators Program позволяет любому желающему интегрировать Xbox Live в свою игру и опубликовать ее для Xbox One и Windows 10. Процесс сертификации максимально упрощен, и никакого утверждения концепции за пределами стандартной [политики Microsoft Store](https://msdn.microsoft.com/library/windows/apps/dn764944.aspx) не предусмотрено.

В рамках Creators Program вы можете развертывать, проектировать и публиковать свои игры без специального комплекта для разработчиков, используя только оборудование, доступное в розничной продаже. Для начала работы скачайте [приложение Dev Mode Activation](https://docs.microsoft.com/windows/uwp/xbox-apps/devkit-activation) на Xbox One.

Если вы хотите получить доступ к еще большим возможностям Xbox Live, находиться в основном магазине Xbox One или получать специальную маркетинговую поддержку и поддержку при разработке, примите участие в программе [ID@Xbox](https://www.xbox.com/Developers/id).

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Программа Xbox Live Creators Program</td>
        <td><a href="https://developer.microsoft.com/games/xbox/xboxlive/creator">Подробнее о программе Xbox Live Creators Program</a></td>
    </tr>
</table>

#### <a name="idxbox"></a>ID@Xbox

Программа ID@Xbox помогает квалифицированным разработчикам самостоятельно публиковать свои игры для Windows и Xbox One. Если вы хотите создать игру для Xbox One или добавить в свою игру для Windows 10 функциональные возможности Xbox Live, например счет игрока, достижения или списки лидеров, зарегистрируйтесь в ID@Xbox прямо сейчас. Став разработчиком ID@Xbox, вы получите необходимые средства и поддержку для успешного воплощения своих творческих идей. Мы рекомендуем применить ID@Xbox , необходимо сначала зарегистрировать учетную запись разработчика в центре партнеров.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Программа для разработчиков ID@Xbox</td>
        <td><a href="https://go.microsoft.com/fwlink/p/?LinkID=526271">Программа независимых разработчиков для Xbox One</a></td>
    </tr>
    <tr>
        <td>Сайт для потребителей ID@Xbox</td>
        <td><a href="https://www.idatxbox.com/">ID@Xbox</a></td>
    </tr>
</table>

#### <a name="xbox-tools-and-middleware"></a>Средства и ПО промежуточного слоя Xbox

Программа средств и ПО промежуточного слоя Xbox предоставляет лицензии на комплекты разработчиков Xbox профессиональным разработчикам средств и ПО промежуточного слоя для игр. Разработчики, принятые в программу, могут совместно использовать и распространять свои технологии Xbox XDK среди других лицензированных разработчиков Xbox.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Обратиться в программу средств и ПО промежуточного слоя</td>
        <td><xboxtlsm@microsoft.com></td>
    </tr>
</table>


### <a name="game-samples"></a>Примеры игр

Имеется большое количество примеров игр и приложений для Windows 10, которые помогут вам понять возможности игр для Windows 10 и быстро освоить процесс разработки игр. Постоянно разрабатываются и публикуются новые примеры, поэтому не забывайте периодически проверять обновления на порталах с примерами. Также можно [следить](https://help.github.com/articles/watching-repositories/) за репозиториями GitHub, чтобы получать информацию об изменениях и дополнениях.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Примеры приложений универсальной платформы Windows</td>
        <td><a href="https://github.com/Microsoft/Windows-universal-samples">Windows-universal-samples</a></td>
    </tr>
    <tr>
        <td>Примеры графики Direct3D 12</td>
        <td><a href="https://github.com/Microsoft/DirectX-Graphics-Samples">DirectX-Graphics-Samples</a></td>
    </tr>
    <tr>
        <td>Примеры графики Direct3D 11</td>
        <td><a href="https://github.com/walbourn/directx-sdk-samples">directx-sdk-samples</a></td>
    </tr>
    <tr>
        <td>Пример игры Direct3D 11 от первого лица</td>
        <td><a href="tutorial--create-your-first-uwp-directx-game.md">Создание простой игры UWP с использованием DirectX</a></td>
    </tr>
    <tr>
        <td>Пример эффектов пользовательского изображения Direct2D</td>
        <td><a href="https://go.microsoft.com/fwlink/p/?LinkId=620531">D2DCustomEffects</a></td>
    </tr>
    <tr>
        <td>Пример сетки градиента Direct2D</td>
        <td><a href="https://go.microsoft.com/fwlink/p/?LinkId=620532">D2DGradientMesh</a></td>
    </tr>
    <tr>
        <td>Пример корректировки фотографии Direct2D</td>
        <td><a href="https://go.microsoft.com/fwlink/p/?LinkId=620533">D2DPhotoAdjustment</a></td>
    </tr>
    <tr>
        <td>Открытые примеры, опубликованные группой Xbox Advanced Technology Group</td>
        <td><a href="https://github.com/Microsoft/Xbox-ATG-Samples">Xbox-ATG-Samples</a></td>
    </tr>
    <tr>
        <td>Примеры для Xbox Live</td>
        <td><a href="https://github.com/Microsoft/xbox-live-samples">xbox-live-samples</a></td>
    </tr>
    <tr>
        <td>Примеры игр для Xbox One (XGD)</td>
        <td><a href="https://developer.microsoft.com/games/xbox/partner/development-education-samples">Примеры</a></td>
    </tr>
    <tr>
        <td>Примеры игр для Windows (коллекция исходных кодов MSDN)</td>
        <td><a href="https://code.msdn.microsoft.com/windowsapps/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=game&f%5B1%5D.Type=Contributors&f%5B1%5D.Value=Microsoft&f%5B1%5D.Text=Microsoft">Примеры игр из Microsoft Store</a></td>
    </tr>
    <tr>
        <td>Пример 2D-игры на JavaScript</td>
        <td><a href="../get-started/get-started-tutorial-game-js2d.md">Создание игры UWP на языке JavaScript</a></td>
    </tr>
    <tr>
        <td>Пример 3D-игры на JavaScript</td>
        <td><a href="../get-started/get-started-tutorial-game-js3d.md">Создание 3D-игры на языке JavaScript с использованием библиотеки three.js</a></td>
    </tr>
    <tr>
        <td>Пример 2D-игры UWP на MonoGame</td>
        <td><a href="../get-started/get-started-tutorial-game-mg2d.md">Создание игры UWP на 2D-платформе MonoGame</a></td>
    </tr>      
</table>


### <a name="developer-forums"></a>Форумы разработчиков

Форумы разработчиков — замечательное место для вопросов, связанных с разработкой игр, получения ответов на них и установления связей с сообществом разработчиков игр. Форумы также могут стать отличнейшим источником уже готовых ответов на сложные вопросы, с которыми разработчики сталкивались ранее и успешно решали.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Публикация приложений и игр, форумы разработчиков</td>
        <td><a href="https://social.msdn.microsoft.com/Forums/en-us/home?category=windowsapps">Публикация и реклама в приложении</a></td>
    </tr>
    <tr>
        <td>Форум разработчиков приложений UWP</td>
        <td><a href="https://social.msdn.microsoft.com/Forums/en-us/home?forum=wpdevelop">Разработка приложений универсальной платформы Windows</a></td>
    </tr>
    <tr>
        <td>Форумы разработчиков классических приложений</td>
        <td><a href="https://social.msdn.microsoft.com/Forums/en-us/home?category=windowsdesktopdev">Форумы, посвященные классическим приложениям для Windows</a></td>
    </tr>
    <tr>
        <td>Игры Microsoft Store на базе DirectX (архив записей форума)</td>
        <td><a href="https://social.msdn.microsoft.com/Forums/vstudio/home?forum=wingameswithdirectx">Создание игр Microsoft Store на базе DirectX (архив)</a></td>
    </tr>
    <tr>
        <td>Форум разработчиков для управляемых партнеров Windows 10</td>
        <td><a href="https://aka.ms/win10devforums">Форумы разработчиков для XBOX: Windows 10</a></td>
    </tr>
    <tr>
        <td>Форумы DirectX</td>
        <td><a href="https://forums.directxtech.com/index.php">Форум по DirectX 12</a></td>
    </tr>
    <tr>
        <td>Форумы по платформе Azure</td>
        <td><a href="https://social.msdn.microsoft.com/Forums/en-us/home?category=windowsazureplatform">Форум Azure</a></td>
    </tr>
    <tr>
        <td>Форум Xbox Live</td>
        <td><a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=xboxlivedev">Форум по разработке для Xbox Live</a></td>
    </tr>
    <tr>
        <td>Форумы PlayFab</td>
        <td><a href="https://community.playfab.com/index.html">Форумы по PlayFab</a></td>
    </tr>
</table>


### <a name="developer-blogs"></a>Блоги для разработчиков

Блоги для разработчиков — еще один замечательный источник самых последних сведений о разработке игр. Здесь вы найдете записи о новых возможностях, подробностях реализации, рекомендуемых приемах, архитектуре и многом другом.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Блог «Создание приложений для Windows»</td>
        <td><a href="https://blogs.windows.com/buildingapps/">Создание приложений для Windows</a></td>
    </tr>
    <tr>
        <td>Windows 10 (записи в блоге)</td>
        <td><a href="https://blogs.windows.com/blog/tag/windows-10/">Записи в блоге Windows 10</a></td>
    </tr>
    <tr>
        <td>Блог группы разработчиков Visual Studio</td>
        <td><a href="https://blogs.msdn.com/b/visualstudio/">Блог Visual Studio</a></td>
    </tr>
    <tr>
        <td>Блоги о средствах разработчика Visual Studio</td>
        <td><a href="https://blogs.msdn.com/b/developer-tools/">Блоги о средствах разработчика</a></td>
    </tr>
    <tr>
        <td>Блог о средствах разработчика Somasegar</td>
        <td><a href="https://blogs.msdn.com/b/somasegar/">Блог Somasegar</a></td>
    </tr>
    <tr>
        <td>Блог разработчиков DirectX</td>
        <td><a href="https://blogs.msdn.com/b/directx">Блог разработчиков DirectX</a></td>
    </tr>
    <tr>
        <td>Введение в DirectX 12 (запись в блоге)</td>
        <td><a href="https://blogs.msdn.com/b/directx/archive/2014/03/20/directx-12.aspx">DirectX12</a></td>
    </tr>
    <tr>
        <td>Блог группы разработчиков средств Visual C++</td>
        <td><a href="https://blogs.msdn.com/b/vcblog/">Блог рабочей группы Visual C++</a></td>
    </tr>
    <tr>
        <td>Блог группы PIX</td>
        <td><a href="https://blogs.msdn.microsoft.com/pix/">Оптимизация производительности и отладка игр на основе DirectX 12 для Windows и Xbox</a></td>
    </tr>
    <tr>
        <td>Блог группы развертывания универсальных приложений для Windows</td>
        <td><a href="https://blogs.msdn.microsoft.com/appinstaller/">Блог группы создания и развертывания приложений UWP</a></td>
    </tr>
</table>
 

## <a name="concept-and-planning"></a>Концепция и планирование


На этапе разработки концепции и планирования вы решаете, какой будет ваша игра и какие технологии и инструменты будут использованы.

### <a name="overview-of-game-development-technologies"></a>Обзор технологий разработки игр

Приступая к разработке игры для UWP, вы можете воспользоваться различными вариантами графики, средств ввода, звука, работы в сети, служебных программ и библиотек.

Если вы уже решили, какие технологии будете использовать в игре, то отлично! Если нет, обратитесь к руководству [Технологии игр для приложений UWP](game-development-platform-guide.md), которое содержит отличный обзор множества доступных технологий. Мы настоятельно рекомендуем ознакомиться с ним, чтобы узнать об имеющихся возможностях и их совместимости друг с другом.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Обзор технологий игр UWP</td>
        <td><a href="game-development-platform-guide.md">Технологии игр для приложений UWP</a></td>
    </tr>
</table>
 

Эти три видео с конференции GDC 2015 содержат хороший обзор разработки игр для Windows 10 и игрового взаимодействия в Windows 10.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Обзор разработки игр для Windows 10 (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/Developing-Games-for-Windows-10">Разработка игр для Windows10</a></td>
    </tr>
    <tr>
        <td>Игровое взаимодействие в Windows 10 (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/Gaming-Consumer-Experience-on-Windows-10">Игровое взаимодействие с пользователем в Windows 10</a></td>
    </tr>
    <tr>
        <td>Игры в экосистеме Microsoft (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/The-Future-of-Gaming-Across-the-Microsoft-Ecosystem">Будущее игр в экосистеме Microsoft</a></td>
    </tr>
</table>

### <a name="game-planning"></a>Планирование игр

Вот некоторые общие концепции и понятия, которые необходимо принимать во внимание при планировании игры.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Создание игры с поддержкой специальных возможностей</td>
        <td><a href="https://msdn.microsoft.com/windows/uwp/gaming/accessibility-for-games">Специальные возможности для игр</a></td>
    </tr>
    <tr>
        <td>Создание игр с помощью облака</td>
        <td><a href="https://msdn.microsoft.com/windows/uwp/gaming/cloud-for-games">Облако для игр</a></td>
    </tr>
    <tr>
        <td>Монетизация игры</td>
        <td><a href="https://msdn.microsoft.com/windows/uwp/gaming/monetization-for-games">Монетизация для игр</a></td>
    </tr>
</table>



### <a name="choosing-your-graphics-technology-and-programming-language"></a>Выбор графической технологии и языка программирования

Существуют несколько языков программирования и графических технологий, доступных для использования в играх для Windows 10. Избранный вариант зависит от типа разрабатывающейся игры, опыта и предпочтений студии разработки, а также специальных функциональных требований игры. Будете ли вы использовать C#, C++ или JavaScript? DirectX, XAML или HTML5?

#### <a name="directx"></a>DirectX

Microsoft DirectX — это технология, которая оптимально подходит для двух- и трехмерной графики и мультимедиа с наивысшей производительностью.

DirectX 12 работает быстрее и эффективнее любой предыдущей версии. Direct3D12 позволяет создавать более сложные сцены и эффекты, увеличить количество объектов и полностью использовать ресурсы современных GPU на компьютерах под управлением Windows 10 и Xbox One.

Если вы хотите использовать знакомый графический конвейер Direct3D 11, вы все равно сможете воспользоваться преимуществами новых функций рендеринга и оптимизации, добавленных в Direct3D 11.3. А если вы проверенный временем разработчик классического API Windows, начавший работу с Win32, вы по-прежнему будете иметь эту возможность в Windows 10.

Исчерпывающий набор функций и глубокая интеграция платформы DirectX обеспечивают мощность и производительность для наиболее требовательных игр.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>DirectX для UWP-разработки</td>
        <td><a href="directx-programming.md">Программирование в DirectX</a></td>
    </tr>
    <tr>
        <td>Учебник: как создать игру UWP на базе DirectX</td>
        <td><a href="tutorial--create-your-first-uwp-directx-game.md">Создание простой игры UWP с использованием DirectX</a></td>
    </tr>
    <tr>
        <td>Обзоры и справочник по DirectX</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/ee663274">Графика и игры на основе DirectX</a></td>
    </tr>
    <tr>
        <td>Справочник и руководство по программированию для Direct3D12</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/dn903821">Графика Direct3D 12</a></td>
    </tr>
    <tr>
        <td>Видео о разработке графики и DirectX 12 (канал YouTube)</td>
        <td><a href="https://www.youtube.com/channel/UCiaX2B8XiXR70jaN7NK-FpA">Обучение Microsoft DirectX 12 и графике</a></td>
    </tr>
</table>
 

#### <a name="xaml"></a>XAML

XAML — это простой в использовании декларативный язык пользовательского интерфейса с такими удобными функциями, как анимации, раскадровки, привязка данных, масштабируемая векторная графика, динамическое изменение размера и графы сцен. XAML отлично подходит для пользовательского интерфейса игры, меню, спрайтов и двухмерной графики. Для упрощения создания макета пользовательского интерфейса XAML совместим с такими средствами оформления и разработки, как Expression Blend и Microsoft Visual Studio. XAML часто используется с C#, но C++ также хорошо подходит, если это ваш предпочитаемый язык или если у игры высокие требования к ЦП.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Обзор платформы XAML</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt228259">Платформа XAML</a></td>
    </tr>
    <tr>
        <td>Пользовательский интерфейс и элементы управления XAML</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt228348">Элементы управления, макеты и текст</a></td>
    </tr>
</table>
 

#### <a name="html-5"></a>HTML 5

HyperText Markup Language (HTML) — это общий язык разметки пользовательского интерфейса, который используется для веб-страниц, приложений и полнофункциональных клиентов. Игры Windows могут использовать HTML5 в качестве полнофункционального уровня представления данных со знакомыми функциями HTML, доступом к платформе универсальных приложений для Windows и поддержкой современных веб-функций, таких как AppCache, рабочие веб-процессы, Canvas, перетаскивание, асинхронное программирование и SVG. Рендеринг HTML сам использует преимущества аппаратного ускорения DirectX, таким образом вы можете получить преимущества производительности DirectX, не создавая при этом дополнительный код. HTML5 следует выбирать, если вы профессионально занимаетесь веб-разработкой, переносом веб-игры или хотите использовать язык и уровни графики, которые легче использовать, чем другие варианты. HTML5 используется вместе с JavaScript, но также может вызывать компоненты, созданные с помощью C# или C++/CX.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Сведения о HTML5 и объектной модели документов</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/br212882.aspx">Справочник по HTML и модели DOM</a></td>
    </tr>
    <tr>
        <td>Рекомендации W3C по HTML5</td>
        <td><a href="https://go.microsoft.com/fwlink/p/?linkid=221374">HTML5</a></td>
    </tr>
</table>
 

#### <a name="combining-presentation-technologies"></a>Сочетание технологий представления данных

Microsoft DirectX Graphic Infrastructure (DXGI) обеспечивает межпрограммное взаимодействие и совместимость нескольких графических технологий. Для высокопроизводительной графики можно комбинировать XAML и DirectX, используя XAML для меню и других простых элементов пользовательского интерфейса и DirectX для рендеринга комплексных двух- и трехмерных сцен. DXGI также обеспечивает совместимость Direct2D, Direct3D, DirectWrite, DirectCompute и Microsoft Media Foundation.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Руководство и справочник по программированию DirectX Graphic Infrastructure</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/hh404534">DXGI</a></td>
    </tr>
    <tr>
        <td>Сочетание DirectX и XAML</td>
        <td><a href="directx-and-xaml-interop.md">Межпрограммное взаимодействие DirectX и XAML</a></td>
    </tr>
</table>
 

#### <a name="c"></a>C++

C++/CX — это высокопроизводительный язык с небольшим объемом служебных данных, который обеспечивает мощное сочетание скорости, совместимости и платформенного доступа. C++/CX упрощает использование всех больших игровых компонентов в Windows 10, в частности DirectX и Xbox Live. Можно также повторно использовать существующий код и библиотеки C++. На C++/CX создается быстрый внутренний код, который не образует дополнительных затрат в виде сборки мусора, таким образом ваша игра может иметь превосходную производительность и низкое потребление питание, продлевая тем самым время работы батареи. Используйте C++/CX с DirectX или XAML либо создайте игру, в которой используется их комбинация.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Обзоры и справочник по C++/CX</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/hh699871.aspx">Справочник по языку Visual C++ (C++/CX)</a></td>
    </tr>
    <tr>
        <td>Справочник и руководство по программированию на Visual C++</td>
        <td><a href="https://docs.microsoft.com/cpp/visual-cpp-in-visual-studio">Visual C++ в Visual Studio 2017</a></td>
    </tr>
</table>
 

#### <a name="c"></a>C#

C# (произносится «Си-шарп») — это современный инновационный язык, который является простым, мощным, типобезопасным и объектно-ориентированным. C# позволяет ускорить разработку, сохраняя понимание и выразительность языков группы C. Несмотря на простоту использования, C# обладает различными расширенными функциями языка, такими как полиморфизм, делегаты, лямбда-выражения, замыкания, методы итераторов, ковариации, а также выражения языка интегрированных запросов (LINQ). C# — идеальный выбор, если вы хотите использовать XAML, быстро начать разрабатывать игру или имеете опыт работы с C#. C# в основном используется вместе с XAML, поэтому, если вы хотите использовать DirectX, выберите C++ или напишите часть игры в виде компонента C++, который взаимодействует с DirectX. Также можно использовать [Win2D](https://github.com/Microsoft/Win2D) — библиотеку графики непосредственного режима Direct2D для C# и C++.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Справочник и руководство по программированию для C#</td>
        <td><a href="https://msdn.microsoft.com/library/kx37x362.aspx">Справочник по языку C#</a></td>
    </tr>
</table>
 

#### <a name="javascript"></a>JavaScript

JavaScript — это динамический язык сценариев, который широко используется в современных веб-приложениях и полнофункциональных клиентах.

Приложения Windows JavaScript имеют доступ к полезным функциям платформы универсальных приложений для Windows простым и интуитивным способом —в качестве методов и свойств объектно-ориентированных классов JavaScript. JavaScript следует выбрать, если у вас есть опыт веб-разработки, вы уже знакомы с JavaScript или хотите использовать библиотеки HTML5, CSS, WinJS или JavaScript. Если вы хотите использовать DirectX или XAML, выберите C# или C++/CX.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Справочник по JavaScript и среде выполнения Windows</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/jj613794">Справка по JavaScript</a></td>
    </tr>
</table>


#### <a name="use-windows-runtime-components-to-combine-languages"></a>Использование компонентов среды выполнения Windows для комбинирования языков

При использовании универсальной платформы Windows можно легко комбинировать компоненты, написанные на разных языках. Создайте компоненты среды выполнения Windows на C++, C# или Visual Basic, а затем вызовите их из JavaScript, C#, C++ или Visual Basic. Это отличный способ программирования частей игры на выбранном вами языке. Компоненты также позволяют использовать внешние библиотеки, доступные только на определенном языке, а также использовать уже написанный устаревший код.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Создание компонентов среды выполнения Windows</td>
        <td><a href="https://docs.microsoft.com/windows/uwp/winrt-components/creating-windows-runtime-components-in-cpp">Создание компонентов среды выполнения Windows</a></td>
    </tr>
</table>


### <a name="which-version-of-directx-should-your-game-use"></a>Какая версия DirectX должна использоваться в вашей игре?

При использовании DirectX для вашей игры, необходимо выбрать версию, которая для использования: Microsoft Direct3D12 или Direct3D11 корпорации Майкрософт.

DirectX 12 работает быстрее и эффективнее любой предыдущей версии. Direct3D12 позволяет создавать более сложные сцены и эффекты, увеличить количество объектов и полностью использовать ресурсы современных GPU на компьютерах под управлением Windows 10 и Xbox One. Так как Direct3D 12 работает на очень низком уровне, с его помощью группа профессиональных разработчиков графики или DirectX 11 может получить все необходимое для максимальной оптимизации графики.

Direct3D 11.3— это низкоуровневый графический API, использующий знакомую модель программирования Direct3D. Он берет на себя больше сложных операций, связанных с отрисовкой GPU. Он также поддерживается в Windows 10 и на Xbox One. Если у вас уже есть графическая подсистема на базе Direct3D 11 и вы еще не готовы перейти на Direct3D 12, вы можете использовать Direct3D 11 на 12, чтобы добиться частичного повышения производительности. Версии от 11.3 и выше включают новые функции отрисовки и оптимизации, которые также доступны в Direct3D 12.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Выбор Direct3D12 или Direct3D11</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/dn899228">Что такое Direct3D12?</a></td>
    </tr>
    <tr>
        <td>Общие сведения о Direct3D11</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/ff476080">Графика Direct3D 11</a></td>
    </tr>
    <tr>
        <td>Обзор Direct3D 11 на 12</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/dn913195">Direct3D 11 на 12</a></td>
    </tr>
</table>


### <a name="bridges-game-engines-and-middleware"></a>Мосты, игровые движки и ПО промежуточного слоя

В зависимости от потребностей вашей игры, использование мостов, игровых движков или ПО промежуточного слоя может сэкономить время и ресурсы, затрачиваемые на разработку и тестирование. Предлагаем вашему вниманию некоторые обзоры и ресурсы по мостам, игровым движкам и ПО промежуточного слоя.

#### <a name="universal-windows-platform-bridges"></a>Мосты универсальной платформы Windows

Мосты универсальной платформы Windows — это технологии, которые делают имеющиеся приложение или игру доступными на UWP. Мосты — это замечательный способ быстро начать разработку игр для UWP.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Мосты UWP</td>
        <td><a href="https://dev.windows.com/bridges/">Используйте свой код при разработке для Windows</a></td>
    </tr>
    <tr>
        <td>Мост Windows для iOS</td>
        <td><a href="https://dev.windows.com/bridges/ios">Перенос приложений для iOS в Windows</a></td>
    </tr>
    <tr>
        <td>Мост Windows для классических приложений (.NET и Win32)</td>
        <td><a href="https://developer.microsoft.com/windows/bridges/desktop">Преобразование классического приложения в приложение UWP</a></td>
    </tr>
</table>

#### <a name="playfab"></a>PlayFab

PlayFab теперь входит в семейство решений Майкрософт и представляет собой полноценную серверную платформу для игр реального времени, которая помогает независимым студиям выйти на рынок. Увеличивайте выручку, привлекайте больше и удерживайте больше пользователей, одновременно сокращая расходы за счет игровых служб, аналитики реального времени и технологии LiveOps.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>PlayFab</td>
        <td><a href="https://playfab.com/">Обзор служб и инструментов</a></td>
    </tr>
    <tr>
        <td>Начало работы</td>
        <td><a href="https://api.playfab.com/docs/general-getting-started">Общее руководство по началу работы</a></td>
    </tr>
    <tr>
        <td>Серия видеоучебников</td>
        <td><a href="https://www.youtube.com/watch?v=fGNpiqVi5xU&list=PLHCfyL7JpoPbLpA_oh_T5PKrfzPgCpPT5">Серия демонстрационных видео об основных системах PlayFab</a></td>
    </tr>
    <tr>
        <td>Рецепты</td>
        <td><a href="https://api.playfab.com/docs/tutorials/recipes-index">Популярные примеры игровой механики и дизайнерских решений</a></td>
    </tr>
    <tr>
        <td>Платформы</td>
        <td><a href="https://api.playfab.com/platforms">Конкретная документация для различных платформ и игровых движков</a></td>
    </tr>
    <tr>
        <td>Репозиторий GitHub</td>
        <td><a href="https://github.com/PlayFab">Сценарии и пакеты SDK для различных платформ, включая Android, iOS, Windows, Unity и Unreal</a></td>
    </tr>
    <tr>
        <td>Документация по API</td>
        <td><a href="https://api.playfab.com/documentation/">Доступ к службе PlayFab напрямую через веб-API, сходные с REST</a></td>
    </tr>
    <tr>
        <td>Форумы</td>
        <td><a href="https://community.playfab.com/index.html">Форумы по PlayFab</a></td>
    </tr>
</table>
 

#### <a name="unity"></a>Unity

Unity предоставляет платформу для создания красивых и увлекательных игр и приложений: двумерных, трехмерных, виртуальной реальности и дополненной реальности. Эта платформа позволяет вам быстро воплотить свое творческое видение и сделать результат доступным практически на любом носителе или устройстве.

Платформа Unity поддерживает разработку в Direct3D 12 (начиная с версии Unity 5.4).

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Игровая подсистема Unity</td>
        <td><a href="https://unity3d.com/">Unity — игровой движок</a></td>
    </tr>
    <tr>
        <td>Получить Unity</td>
        <td><a href="https://unity3d.com/get-unity">Получить Unity</a></td>
    </tr>
    <tr>
        <td>Документация по Unity для Windows</td>
        <td><a href="https://docs.unity3d.com/Manual/Windows.html">Руководство по Unity для Windows</a></td>
    </tr>
    <tr>
        <td>Добавление технологии LiveOps с использованием PlayFab</td>
        <td><a href="https://api.playfab.com/docs/getting-started/unity-getting-started">Начало работы: первый вызов API PlayFab из игры Unity</a></td>
    </tr>
    <tr>
        <td>Добавление интерактивных возможностей в игру с помощью Mixer Interactive</td>
        <td><a href="https://github.com/mixer/interactive-unity-plugin/wiki/Getting-started">Руководство по началу работы</a></td>
    </tr>
    <tr>
        <td>Пакет Mixer SDK для Unity</td>
        <td><a href="https://www.assetstore.unity3d.com/en/#!/content/88585">Подключаемый модуль Mixer Unity</a></td>
    </tr>
    <tr>
        <td>Справочная документация по Mixer SDK для Unity</td>
        <td><a href="https://dev.mixer.com/reference/interactive/csharp/index.html">Справочник по API для подключаемого модуля Mixer Unity</a></td>
    </tr>
    <tr>
        <td>Публикация игры Unity в Microsoft Store</td>
        <td><a href="https://unity3d.com/partners/microsoft/porting-guides">Руководство по портированию</a></td>
    </tr>
    <tr>
        <td>Устранение неполадок с отсутствием ссылок на сборки, связанные с API-интерфейсами .NET</td>
        <td><a href="https://docs.microsoft.com/windows/uwp/gaming/missing-dot-net-apis-in-unity-and-uwp">Отсутствующие API-интерфейсы .NET в Unity и UWP</a></td>
    </tr>
    <tr>
        <td>Публикация игры Unity как приложения универсальной платформы Windows (видео)</td>
        <td><a href="https://channel9.msdn.com/Blogs/One-Dev-Minute/How-to-publish-your-Unity-game-as-a-UWP-app">Как опубликовать игру Unity в качестве приложения UWP</a></td>
    </tr>
    <tr>
        <td>Использование Unity для создания игр и приложений для Windows (видео)</td>
        <td><a href="https://channel9.msdn.com/Blogs/One-Dev-Minute/Making-games-and-apps-with-Unity">Создание игр и приложений для Windows с помощью Unity</a></td>
    </tr>
    <tr>
        <td>Разработка игры Unity с помощью Visual Studio (серия видео)</td>
        <td><a href="https://go.microsoft.com/fwlink/?LinkId=722359">Использование Unity с Visual Studio 2015</a></td>
    </tr>
</table>
 

#### <a name="havok"></a>Havok

Модульный набор средств и технологий Havok помогает создателям игр выходить на новые уровни взаимодействия и погружения. Havok обеспечивает высокореалистичную физику, возможность интерактивного моделирования и потрясающие кинематографические сцены. В версии 2015.1 и более поздних версиях Visual Studio 2015 на базе архитектур x86, x64 и ARM официально поддерживается UWP.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Веб-сайт Havok</td>
        <td><a href="https://www.havok.com/">Havok</a></td>
    </tr>
    <tr>
        <td>Набор средств Havok</td>
        <td><a href="https://www.havok.com/products/">Обзор продуктов Havok</a></td>
    </tr>
    <tr>
        <td>Форумы технической поддержки Havok</td>
        <td><a href="https://support.havok.com">Havok</a></td>
    </tr>
</table>
 

#### <a name="monogame"></a>MonoGame

MonoGame —это кроссплатформенное решение с открытым исходным кодом для разработки игр, основанное на Microsoft XNA Framework 4.0. Monogame в настоящее время поддерживает Windows, Windows Phone и Xbox, а также Linux, macOS, iOS, Android и несколько других платформ.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>MonoGame</td>
        <td><a href="https://www.monogame.net">Веб-сайт MonoGame</a></td>
    </tr>
    <tr>
        <td>Документация по MonoGame</td>
        <td><a href="https://www.monogame.net/documentation/">Документация MonoGame (последняя)</a></td>
    </tr>
    <tr>
        <td>Скачиваемые файлы Monogame</td>
        <td><a href="https://www.monogame.net/downloads/">Скачайте выпуски, сборки для разработчиков и исходный код</a> с веб-сайта MonoGame или <a href="https://www.nuget.org/profiles/MonoGame">получите последний выпуск через NuGet</a>.
    </tr>
    <tr>
        <td>Пример 2D-игры UWP на MonoGame</td>
        <td><a href="../get-started/get-started-tutorial-game-mg2d.md">Создание игры UWP на 2D-платформе MonoGame</a></td>
    </tr>    
</table>


#### <a name="cocos2d"></a>Cocos2d

Cocos2d-x — это кроссплатформенный набор средств и движков для разработки игр с открытым кодом, поддерживающий разработку игр UWP. Начиная с версии 3 добавляются также возможности для 3D-разработки.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Cocos2d-x</td>
        <td><a href="https://www.cocos2d-x.org/">Что такое Cocos2d-x?</a></td>
    </tr>
    <tr>
        <td>Руководство по Cocos2d-x для программистов</td>
        <td><a href="https://www.cocos2d-x.org/programmersguide/">Руководство по программированию в Cocos2d-x</a></td>
    </tr>
    <tr>
        <td>Cocos2d-x в Windows 10 (запись в блоге)</td>
        <td><a href="https://blogs.windows.com/buildingapps/2015/06/15/running-cocos2d-x-on-windows-10/">Запуск Cocos2d-x в Windows 10</a></td>
    </tr>
    <tr>
        <td>Добавление технологии LiveOps с использованием PlayFab</td>
        <td><a href="https://api.playfab.com/docs/getting-started/cocos2d-x-getting-started-guide">Начало работы: первый вызов API PlayFab из игры Cocos2d</a></td>
    </tr>
</table>


#### <a name="unreal-engine"></a>Unreal Engine

Unreal Engine4— это полный набор средств разработки любых игр любыми разработчиками. Unreal Engine используется разработчиками игр по всему миру для наиболее требовательных игр для консолей и компьютеров.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Обзор Unreal Engine</td>
        <td><a href="https://www.unrealengine.com/what-is-unreal-engine-4">Unreal Engine 4</a></td>
    </tr>
    <tr>
        <td>Добавление технологии LiveOps с использованием PlayFab — C++</td>
        <td><a href="https://api.playfab.com/docs/getting-started/unreal-cpp-getting-started">Начало работы: первый вызов API PlayFab из игры Unreal</a></td>
    </tr>
    <tr>
        <td>Добавление технологии LiveOps с использованием PlayFab — схемы</td>
        <td><a href="https://api.playfab.com/docs/getting-started/unreal-blueprints-getting-started">Начало работы: первый вызов API PlayFab из игры Unreal</a></td>
    </tr>
</table>

#### <a name="babylonjs"></a>BabylonJS

BabylonJS — это комплексная платформа JavaScript для создания трехмерных игр с использованием HTML5, WebGL, WebVR и веб-аудио.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>BabylonJS</td>
        <td><a href="https://www.babylonjs.com/">BabylonJS</a></td>
    </tr>
    <tr>
        <td>WebGL 3D с HTML5 и BabylonJS (серия видео)</td>
        <td><a href="https://channel9.msdn.com/Series/Introduction-to-WebGL-3D-with-HTML5-and-Babylonjs/01">Изучение WebGL 3D и BabylonJS</a></td>
    </tr>
    <tr>
        <td>Создание кросс-платформенной игры WebGL с помощью BabylonJS</td>
        <td><a href="https://www.smashingmagazine.com/2016/07/babylon-js-building-sponza-a-cross-platform-webgl-game/">Использование BabylonJS для разработки кросс-платформенной игры</a></td>
    </tr>    
</table>

### <a name="porting-your-game"></a>Портирование игры

Если у вас уже есть игра, существует множество ресурсов и руководств, которые помогут быстро сделать игру доступной на UWP. Для ускорения процесса переноса можно также попробовать использовать [Мост универсальной платформы Windows](#universal-windows-platform-bridges).

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Перенос приложения для Windows 8 на универсальную платформу Windows</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt238322">Переход со среды выполнения Windows 8.x на UWP</a></td>
    </tr>
    <tr>
        <td>Перенос приложения для Windows 8 на универсальную платформу Windows (видео)</td>
        <td><a href="https://channel9.msdn.com/Series/A-Developers-Guide-to-Windows-10/21">Перенос приложений 8.1 в Windows 10</a></td>
    </tr>
    <tr>
        <td>Перенос приложения iOS на универсальную платформу Windows</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt238320">Переход с iOS на UWP</a></td>
    </tr>
    <tr>
        <td>Перенос приложения Silverlight на универсальную платформу Windows</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt238323">Переход с WindowsPhoneSilverlight на UWP</a></td>
    </tr>
    <tr>
        <td>Перенос приложения с XAML или Silverlight на универсальную платформу Windows (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/Build/2015/3-741">Перенос приложения из XAML или Silverlight в Windows 10</a></td>
    </tr>
    <tr>
        <td>Перенос игры для Xbox на универсальную платформу Windows</td>
        <td><a href="https://developer.xboxlive.com/en-us/platform/development/education/Documents/Porting%20from%20Xbox%20One%20to%20Windows%2010.aspx">Перенос из Xbox One в UWP для Windows 10</a></td>
    </tr>
    <tr>
        <td>Перенос из DirectX 9 в DirectX 11</td>
        <td><a href="porting-your-directx-9-game-to-windows-store.md">Перенос из DirectX9 на универсальную платформу Windows (UWP)</a></td>
    </tr>
    <tr>
        <td>Перенос из Direct3D 11 в Direct3D 12</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/mt431709">Перенос из Direct3D 11 в Direct3D 12</a></td>
    </tr>
    <tr>
        <td>Перенос из OpenGL ESв Direct3D11</td>
        <td><a href="port-from-opengl-es-2-0-to-directx-11-1.md">Перенос из OpenGL ES2.0 в Direct3D11</a></td>
    </tr>
    <tr>
        <td>Перенос из OpenGL ES в Direct3D11 с помощью ANGLE</td>
        <td><a href="https://go.microsoft.com/fwlink/p/?linkid=618387">ANGLE</a></td>
    </tr>
    <tr>
        <td>Эквиваленты классических API для Windows в UWP</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/hh464945">Альтернативы Windows API в приложениях универсальной платформы Windows (UWP)</a></td>
    </tr>
</table>


## <a name="prototype-and-design"></a>Создание прототипа и проектирование


Определив, игру какого типа вы хотите создать и какие средства и графические технологии вы будете использовать в ходе разработки, вы можете приступить к проектированию и созданию прототипа. По своей сути ваша игра является приложением универсальной платформы Windows, поэтому с этого мы и начнем.

### <a name="introduction-to-the-universal-windows-platform-uwp"></a>Введение в универсальную платформу Windows (UWP)

В Windows 10 впервые использована универсальная платформа Windows (UWP), которая предоставляет общую платформу API для разных устройств под управлением Windows 10. UWP является продуктом развития и расширения модели среды выполнения Windows, отточенной до единого унифицированного ядра. Игры для UWP могут вызывать API-интерфейсы WinRT, которые являются общими для всех устройств. Поскольку UWP предоставляет гарантированные уровни API, вы можете создать один пакет приложения, который будет устанавливаться на различных устройствах под управлением Windows 10. А если вы захотите, то игра сможет вызывать API (в том числе некоторые классические API для Windows из Win32 и .NET), относящиеся к устройствам, на которых будет использоваться игра.

Предлагаем вашему вниманию отличные руководства, в которых подробно обсуждаются приложения универсальной платформы Windows. Эти руководства помогут вам лучше понять платформу.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Знакомство с приложениями универсальной платформы Windows (UWP)</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/dn726767">Что такое приложение универсальной платформы Windows?</a></td>
    </tr>
    <tr>
        <td>Обзор UWP</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/dn894631">Руководство по приложениям UWP</a></td>
    </tr>
</table>
 

### <a name="getting-started-with-uwp-development"></a>Начало разработки для UWP

Подготовка к разработке приложения универсальной платформы Windows — быстрый и легкий процесс. В следующих руководствах приведены пошаговые инструкции.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Начало работы по разработке для UWP</td>
        <td><a href="https://dev.windows.com/getstarted">Начало работы с приложениями для Windows</a></td>
    </tr>
    <tr>
        <td>Подготовка к разработке для UWP</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/dn726766">Подготовка</a></td>
    </tr>
</table>

Если вы являетесь абсолютным новичком в программировании для UWP и хотите использовать в своей игре XAML (см. [Выбор графической технологии и языка программирования](#choosing-your-graphics-technology-and-programming-language)), отличным источником информации станет серия видеоматериалов [Разработка для Windows 10 для абсолютных новичков](https://channel9.msdn.com/Series/Windows-10-development-for-absolute-beginners).

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Руководство для начинающих по разработке для Windows 10 с помощью XAML (серия видео)</td>
        <td><a href="https://channel9.msdn.com/Series/Windows-10-development-for-absolute-beginners">Разработка для Windows 10 для абсолютных новичков</a></td>
    </tr>
    <tr>
        <td>Объявление о выходе серии видео о разработке для Windows 10 с помощью XAML для абсолютных новичков (запись в блоге)</td>
        <td><a href="https://blogs.windows.com/buildingapps/2015/09/30/windows-10-development-for-absolute-beginners/">Разработка для Windows 10 для абсолютных новичков</a></td>
    </tr>
</table>

### <a name="uwp-development-concepts"></a>Принципы разработки для UWP

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Обзор разработки приложений универсальной платформы Windows</td>
        <td><a href="https://dev.windows.com/develop">Разработка приложений для Windows</a></td>
    </tr>
    <tr>
        <td>Обзор сетевого программирования в UWP</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt280378">Работа в сети и веб-службы</a></td>
    </tr>
    <tr>
        <td>Применение Windows.Web.HTTP и Windows.Networking.Sockets в играх</td>
        <td><a href="work-with-networking-in-your-directx-game.md">Поддержка сети в играх</a></td>
    </tr>
    <tr>
        <td>Концепции асинхронного программирования в UWP</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt187335">Асинхронное программирование</a></td>
    </tr>
</table>

### <a name="windows-desktop-apisto-uwp"></a>UWP APIsto рабочего стола Windows

Информация по этим ссылкам упростит перенос классической игры Windows на UWP.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Использование существующего кода C++ для разработки игр UWP</td>
        <td><a href="https://docs.microsoft.com/cpp/porting/how-to-use-existing-cpp-code-in-a-universal-windows-platform-app">Инструкции: использование существующего кода C++ в приложении UWP</a></td>
    </tr>
    <tr>
        <td>API-интерфейсы UWP для API-интерфейсов Win32 и COM</td>
        <td><a href="https://docs.microsoft.com/uwp/win32-and-com/win32-and-com-for-uwp-apps">API Win32 и COM для приложений UWP</a></td>
    </tr>
    <tr>
        <td>Неподдерживаемые функции CRT в UWP</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/jj606124.aspx">Функции CRT, не поддерживаемые в приложениях универсальной платформы Windows</a></td>
    </tr>
    <tr>
        <td>Альтернативы API Windows</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt592894.aspx">Альтернативы API Windows в приложениях универсальной платформы Windows (UWP)</a></td>
    </tr>
</table>
 

### <a name="process-lifetime-management"></a>Управление жизненным циклом процесса

Управление жизненным циклом процесса (или жизненный цикл приложения) описывает различные состояния активации, через которые может проходить приложение универсальной платформы Windows. Игру можно активировать, приостановить, возобновить и завершить, а переход между этими состояниями может выполняться различными способами.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Обработка переходов между этапами жизненного цикла приложения</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt243287">Жизненный цикл приложения</a></td>
    </tr>
    <tr>
        <td>Использование Microsoft Visual Studio для активации переходов между этапами приложения</td>
        <td><a href="https://msdn.microsoft.com/library/hh974425.aspx">Активация событий приостановки, возобновления и перевода в фоновый режим для приложений UWP в Visual Studio</a></td>
    </tr>
</table>
 

### <a name="designing-game-ux"></a>Проектирование взаимодействия с пользователем игры

В основе хорошей игры лежит творческое оформление.

Игры и приложения схожи в некоторых стандартных элементах пользовательского интерфейса и принципах оформления, но игры часто характеризуются уникальным внешним видом, пользовательским интерфейсом и дизайном. Игры имеют успех, когда продуманное оформление применяется к обоим аспектам: когда в игре следует использовать проверенный пользовательский интерфейс, а когда он должен отличаться и быть инновационным. Выбранная для игры технология представления данных (DirectX, XAML, HTML5 или определенная комбинация этих трех технологий) может повлиять на детали реализации, однако применяемые принципы оформления почти не зависят от этого выбора.

В отличие от взаимодействия с пользователем, такие аспекты игры, как проектирование уровня, прохождение, проектирование мира и другие, сами по себе являются искусством; здесь решаете только вы и ваша рабочая группа, и эти вопросы не освещаются в данном руководстве по разработке.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Рекомендации и основы проектирования для UWP</td>
        <td><a href="https://developer.microsoft.com/en-us/windows/apps/design">Проектирование приложений UWP</a></td>
    </tr>
    <tr>
        <td>Проектирование этапов жизненного цикла приложения</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/dn611862">Руководство по взаимодействию с пользователем — запуск, приостановка и возобновление работы приложения</a></td>
    </tr>
    <tr>
        <td>Проектирование приложения UWP для Xbox One и телевизоров</td>
        <td><a href="https://docs.microsoft.com/windows/uwp/design/devices/designing-for-tv">Проектирование для Xbox и телевизора</a></td>
    </tr>
    <tr>
        <td>Написание приложений для разных форм-факторов устройств (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/Designing-Games-for-a-Windows-Core-World">Разработка игр мира Windows Core</a></td>
    </tr>   
</table>
 

#### <a name="color-guideline-and-palette"></a>Рекомендации по цвету и цветовой палитре

Следование единообразной цветовой палитре улучшает эстетику игры, упрощает навигацию, а также является мощным инструментом для информирования игрока о функциях меню и HUD. Согласованная расцветка игровых элементов, например предупреждений, ущерба, опыта и достижений, может упростить пользовательский интерфейс и снизить необходимость в явных метках.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Рекомендации по цвету</td>
        <td><a href="https://assets.windowsphone.com/499cd2be-64ed-4b05-a4f5-cd0c9ad3f6a3/101_BestPractices_Color_InvariantCulture_Default.zip">Рекомендации: цвет</a></td>
    </tr>
</table>
 

#### <a name="typography"></a>Шрифтовое оформление

Правильное применение оформления текста улучшает многие аспекты игры, в частности макет пользовательского интерфейса, навигацию, читаемость, атмосферу, фирменный стиль и погружение игрока.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Руководство по оформлению текста</td>
        <td><a href="https://go.microsoft.com/fwlink/?LinkId=535007">Рекомендации: оформление текста</a></td>
    </tr>
</table>
 

#### <a name="ui-map"></a>Карта пользовательского интерфейса

Карта пользовательского интерфейса — это макет навигации и меню игры в виде блок-схемы. Карта пользовательского интерфейса позволяет всем заинтересованным лицам разобраться в интерфейсе и путях навигации игры, а также выявить потенциальные препятствия и тупики на ранней стадии цикла разработки.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Руководство по карте пользовательского интерфейса</td>
        <td><a href="https://go.microsoft.com/fwlink/?LinkId=535008">Рекомендации: карта пользовательского интерфейса</a></td>
    </tr>
</table>

### <a name="game-audio"></a>Звук для игр

Руководства и справочные материалы по реализации звука в играх с помощью XAudio2, XAPO и Windows Sonic. XAudio2— это низкоуровневый звуковой API-интерфейс, который предоставляет базовые возможности обработки сигналов и микширование звука для разработки высокопроизводительных звуковых модулей. API-интерфейс XAPO позволяет создавать межплатформенные объекты для обработки звука (XAPO) для использования в XAudio2 для Windows и Xbox. Windows Sonic позволяет добавить в игры и мультимедиа-приложения поддержку Dolby Atmos for Home Theater, Dolby Atmos for Headphones и Windows HRTF.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>API-интерфейсы XAudio2</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/hh405049.aspx">Руководство по программированию и справочник по API для XAudio2</a></td>
    </tr>
    <tr>
        <td>Создание межплатформенных объектов для обработки звука</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/ee415735.aspx">Обзор протокола XAPO</a></td>
    </tr>
    <tr>
        <td>Введение в понятия обработки звука</td>
        <td><a href="working-with-audio-in-your-directx-game.md">Звук для игр</a></td>
    </tr>
    <tr>
        <td>Обзор Windows Sonic</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/mt807491.aspx">Пространственное звучание</a></td>
    </tr>
    <tr>
        <td>Примеры пространственного звучания Windows Sonic</td>
        <td><a href="https://github.com/Microsoft/Xbox-ATG-Samples/tree/master/UWPSamples/Audio">Звуковые примеры, опубликованные группой Xbox Advanced Technology Group</a></td>
    </tr>
    <tr>
        <td>Узнайте, как интегрировать Windows Sonic в вашей игре (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2017/GDC2017-002">Новые возможности пространственного звука для Xbox andWindows</a></td>
    </tr>
</table>

### <a name="directx-development"></a>Разработка с использованием DirectX

Руководства и справочные материалы по разработке игр на базе DirectX

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>DirectX для UWP-разработки</td>
        <td><a href="directx-programming.md">Программирование в DirectX</a></td>
    </tr>
    <tr>
        <td>Учебник: как создать игру UWP на базе DirectX</td>
        <td><a href="tutorial--create-your-first-uwp-directx-game.md">Создание простой игры UWP с использованием DirectX</a></td>
    </tr>
    <tr>
        <td>Взаимодействие DirectX с моделью приложений UWP</td>
        <td><a href="about-the-uwp-user-interface-and-directx.md">Объект приложения и DirectX</a></td>
    </tr>
    <tr>
        <td>Видео о разработке графики и DirectX 12 (канал YouTube)</td>
        <td><a href="https://www.youtube.com/channel/UCiaX2B8XiXR70jaN7NK-FpA">Обучение Microsoft DirectX 12 и графике</a></td>
    </tr>
    <tr>
        <td>Обзоры и справочник по DirectX</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/ee663274">Графика и игры на основе DirectX</a></td>
    </tr>
    <tr>
        <td>Справочник и руководство по программированию для Direct3D12</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/dn903821">Графика Direct3D 12</a></td>
    </tr>
    <tr>
        <td>Основы DirectX 12 (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/Better-Power-Better-Performance-Your-Game-on-DirectX12">Выше мощность — выше производительность: ваша игра на DirectX 12</a></td>
    </tr>
</table>

#### <a name="learning-direct3d-12"></a>Обучение Direct3D 12

Узнайте, что изменилось в Direct3D 12 и как начать программировать с помощью Direct3D 12. 

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Настройка среды для программирования</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/dn899120.aspx">Настройка среды для программирования с помощью Direct3D 12</a></td>
    </tr>
    <tr>
        <td>Создание базового компонента</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/dn859356.aspx">Создание базового компонента Direct3D 12</a></td>
    </tr>
    <tr>
        <td>Изменения в Direct3D 12</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/dn899194.aspx">Важные изменения при переходе с Direct3D11 на Direct3D12</a></td>
    </tr>
    <tr>
        <td>Перенос из Direct3D 11 в Direct3D 12</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/mt431709.aspx">Перенос из Direct3D 11 в Direct3D 12</a></td>
    </tr>
    <tr>
        <td>Понятия привязки ресурсов (сопроводительный дескриптор, таблица дескрипторов, куча дескрипторов и корневая подпись) </td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/dn899206.aspx">Привязка ресурсов в Direct3D12</a></td>
    </tr>
    <tr>
        <td>Управление памятью</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/dn899198.aspx">Управление памятью в Direct3D12</a></td>
    </tr>
</table>
 

#### <a name="directx-tool-kit-and-libraries"></a>Набор инструментов и библиотеки DirectX

Набор инструментов DirectX, библиотека обработки текстур DirectX, библиотека обработки геометрии DirectXMesh, библиотека UVAtlas и библиотека DirectXMath содержат текстуры, сетки, спрайты и другие служебные функции и вспомогательные классы для разработки DirectX. Эти библиотеки помогают сэкономить время и усилия при разработке.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Получить набор инструментов DirectX для DirectX 11</td>
        <td><a href="https://go.microsoft.com/fwlink/?LinkId=248929">DirectXTK</a></td>
    </tr>
    <tr>
        <td>Получить набор инструментов DirectX для DirectX 12</td>
        <td><a href="https://go.microsoft.com/fwlink/?LinkID=615561">DirectXTK 12</a></td>
    </tr>
    <tr>
        <td>Получить библиотеку обработки текстур DirectX</td>
        <td><a href="https://go.microsoft.com/fwlink/?LinkId=248926">DirectXTex</a></td>
    </tr>
    <tr>
        <td>Получить библиотеку обработки геометрии DirectXMesh</td>
        <td><a href="https://go.microsoft.com/fwlink/?LinkID=324981">DirectXMesh</a></td>
    </tr>
    <tr>
        <td>Получить библиотеку UVAtlas для создания и упаковывания атласа текстуры isochart.</td>
        <td><a href="https://go.microsoft.com/fwlink/?LinkID=512686">UVAtlas</a></td>
    </tr>
    <tr>
        <td>Получить библиотеку DirectXMath</td>
        <td><a href="https://go.microsoft.com/fwlink/?LinkID=615560">DirectXMath</a></td>
    </tr>
    <tr>
        <td>Поддержка Direct3D12 в DirectXTK (запись блога)</td>
        <td><a href="https://github.com/Microsoft/DirectXTK/issues/2">Поддержка DirectX 12</a></td>
    </tr>
</table>

#### <a name="directx-resources-from-partners"></a>Ресурсы DirectX от партнеров

Это дополнительная документация по DirectX, созданная внешними партнерами.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Nvidia: рекомендации по DX12 (запись в блоге) </td>
        <td><a href="https://developer.nvidia.com/dx12-dos-and-donts-updated">DirectX 12 на GPU Nvidia</a></td>
    </tr>
    <tr>
        <td>Intel: эффективная отрисовка с помощью DirectX 12</td>
        <td><a href="https://software.intel.com/sites/default/files/managed/4a/38/Efficient-Rendering-with-DirectX-12-on-Intel-Graphics.pdf">Отрисовка с помощью DirectX 12 на процессорах Intel Graphics</a></td>
    </tr>
    <tr>
        <td>Intel: поддержка нескольких адаптеров в DirectX 12</td>
        <td><a href="https://software.intel.com/articles/multi-adapter-support-in-directx-12">Реализация явного многоадаптерного приложения с помощью DirectX 12</a></td>
    </tr>
    <tr>
        <td>Intel: учебник по DirectX 12</td>
        <td><a href="https://software.intel.com/articles/tutorial-migrating-your-apps-to-directx-12-part-1">Совместный технический документ Intel, Suzhou Snail и Майкрософт</a></td>
    </tr>
</table>


## <a name="production"></a>Создание


Ваша студия теперь полностью задействована и переходит к этапу создания, вся работа распределена среди членов рабочей группы. Вы дорабатываете, перерабатываете и развиваете прототип для превращения его в настоящую игру.

### <a name="notifications-and-live-tiles"></a>Уведомления и живые плитки

Плитка используется для представления игры в меню «Пуск». Плитки и уведомления могут привлечь интерес игроков, даже если они в настоящий момент не играют в вашу игру.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Разработка плиток и индикаторов событий</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt185606">Плитки, индикаторы событий и уведомления</a></td>
    </tr>
    <tr>
        <td>Примеры живых плиток и уведомлений</td>
        <td><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Notifications">Пример уведомлений</a></td>
    </tr>
    <tr>
        <td>Шаблоны адаптивных плиток (запись в блоге)</td>
        <td><a href="https://blogs.msdn.com/b/tiles_and_toasts/archive/2015/06/30/adaptive-tile-templates-schema-and-documentation.aspx">Шаблоны адаптивных плиток: схема и документация</a></td>
    </tr>
    <tr>
        <td>Проектирование плиток и индикаторов событий</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/hh465403">Руководство по плиткам и индикаторам событий</a></td>
    </tr>
    <tr>
        <td>Приложение Windows 10 для интерактивной разработки шаблонов живых плиток</td>
        <td><a href="https://www.microsoft.com/store/apps/9nblggh5xsl1">Визуализатор уведомлений</a></td>
    </tr>
    <tr>
        <td>Расширение генератора плиток UWP для Visual Studio</td>
        <td><a href="https://visualstudiogallery.msdn.microsoft.com/09611e90-f3e8-44b7-9c83-18dba8275bb2">Средство для создания всех необходимых плиток с использованием одного изображения</a></td>
    </tr>
    <tr>
        <td>Расширение генератора плиток UWP для Visual Studio (запись в блоге)</td>
        <td><a href="https://blogs.windows.com/buildingapps/2016/02/15/uwp-tile-generator-extension-for-visual-studio/">Советы по использованию генератора плиток UWP</a></td>
    </tr>
</table>
 

### <a name="enable-in-app-product-add-on-purchases"></a>Поддержка покупок внутренних продуктов приложения (надстройка)

Надстройки (продукта из приложения) — это дополнительный который игроки могут приобрести в игре. Надстройки можно уровнями игры, элементы или все остальное, что может понравиться игрокам. Использовать соответствующим образом, надстроек могут приносить прибыль, при этом улучшая игровой процесс. Определение и публиковать игры надстройки через Центр партнеров и покупок из приложения в коде вашей игры.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Надстройки длительного пользования</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt219684">Поддержка покупки продуктов внутри приложения</a></td>
    </tr>
    <tr>
        <td>Потребляемые надстройки</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt219683">Поддержка покупок потребляемых внутренних продуктов приложения</a></td>
    </tr>
    <tr>
        <td>Сведений о надстройке и отправки</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt148551">Отправка надстроек</a></td>
    </tr>
    <tr>
        <td>Отслеживание продаж надстройки и демографические сведения о вашей игры</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt148538">Отчет о приобретении надстроек</a></td>
    </tr>
</table>
 

### <a name="debugging-performance-optimization-and-monitoring"></a>Отладка, оптимизация и мониторинг производительности

Чтобы оптимизировать производительность, воспользуйтесь преимуществами режима игры в Windows 10, чтобы максимально эффективно использовать текущее оборудование игроков.

Набор средств для оценки производительности Windows (WPT) включает средства мониторинга производительности, создающие подробные профили производительности приложений и операционных систем Windows. Это особенно полезно для контроля за использованием памяти и для повышения производительности игры. Набор средств для оценки производительности Windows включен в Windows 10 SDK и Windows ADK. В этот набор средств входят два независимых средства: Windows Performance Recorder (WPR) и Windows Performance Analyzer (WPA). ProcDump— это программа командной строки, входящая в состав [Windows Sysinternals](https://technet.microsoft.com/sysinternals/default), которая отслеживает скачки использования ЦП и создает файлы дампа во время сбоев игры. 

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Тестирование производительности кода</td>
        <td><a href="https://www.visualstudio.com/team-services/cloud-load-testing/">Нагрузочное тестирование в облаке</a></td>
    </tr>
    <tr>
        <td>Получение типа консоли Xbox с использованием сведений об игровом устройстве</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/mt825235">Сведения об игровом устройстве</a></td>
    </tr>
    <tr>
        <td>Повышение производительности за счет эксклюзивного или приоритетного доступа к аппаратным ресурсам с помощью API-интерфейсов режима игры</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/mt808808">Режим игры</a></td>
    </tr>
    <tr>
        <td>Получить набор средств для оценки производительности Windows (WPT) из Windows 10 SDK</td>
        <td><a href="https://developer.microsoft.com/windows/downloads/windows-10-sdk">Windows 10 SDK</a></td>
    </tr>
    <tr>
        <td>Получить набор средств для оценки производительности Windows (WPT) из Windows ADK</td>
        <td><a href="https://msdn.microsoft.com/windows/hardware/dn913721.aspx">Windows ADK</a></td>
    </tr>
    <tr>
        <td>Устранение с помощью Windows Performance Analyzer проблем, при которых пользовательский интерфейс не отвечает (видео)</td>
        <td><a href="https://channel9.msdn.com/Shows/Defrag-Tools/Defrag-Tools-156-Critical-Path-Analysis-with-Windows-Performance-Analyzer">Анализ методом критического пути с помощью WPA</a></td>
    </tr>
    <tr>
        <td>Определение используемых объемов и потерь памяти с помощью Windows Performance Recorder (видео)</td>
        <td><a href="https://channel9.msdn.com/Shows/Defrag-Tools/Defrag-Tools-154-Memory-Footprint-and-Leaks">Используемый объем памяти и утечки памяти</a></td>
    </tr>
    <tr>
        <td>Получить ProcDump</td>
        <td><a href="https://technet.microsoft.com/sysinternals/dd996900">ProcDump</a></td>
    </tr>
    <tr>
        <td>Указания по использованию ProcDump (видео)</td>
        <td><a href="https://channel9.msdn.com/Shows/Defrag-Tools/Defrag-Tools-131-Windows-10-SDK">Настройка ProcDump для создания файлов дампа</a></td>
    </tr>
</table>

### <a name="advanced-directx-techniques-and-concepts"></a>Современные методы и концепции DirectX

Некоторые тонкие элементы разработки DirectX могут представлять сложность. Если вам необходимо получить дополнительные сведения о вашем движке DirectX или решить сложные проблемы с производительностью, вам могут быть полезны ресурсы и сведения из этого раздела.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>PIX в Windows</td>
        <td><a href="https://blogs.msdn.microsoft.com/pix/2017/01/17/introducing-pix-on-windows-beta/">Средство оптимизации производительности и отладки для DirectX 12 в Windows</a></td>
    </tr>
    <tr>
        <td>Средства отладки и проверки для разработки на базе D3D12 (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2017/GDC2017-003">Настройка производительности и отладка с помощью PIX и GPUValidation D3d12</a></td>
    </tr>
    <tr>
        <td>Оптимизация графики и производительности (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/Advanced-DirectX12-Graphics-and-Performance">Улучшенная графика и производительность DirectX 12</a></td>
    </tr>
    <tr>
        <td>Отладка графики DirectX (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/Solve-the-Tough-Graphics-Problems-with-your-Game-Using-DirectX-Tools">Решение сложных проблем графики игры с помощью средств DirectX</a></td>
    </tr>
    <tr>
        <td>Средства Visual Studio 2015 для отладки DirectX 12 (видео)</td>
        <td><a href="https://channel9.msdn.com/Series/ConnectOn-Demand/212">Средства DirectX для Windows 10 в Visual Studio 2015</a></td>
    </tr>
    <tr>
        <td>Руководство по программированию для Direct3D 12</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/dn903821">Руководство по программированию для Direct3D12</a></td>
    </tr>
    <tr>
        <td>Сочетание DirectX и XAML</td>
        <td><a href="directx-and-xaml-interop.md">Межпрограммное взаимодействие DirectX и XAML</a></td>
    </tr>
</table>

### <a name="high-dynamic-range-hdr-content-development"></a>Разработка содержимого с расширенным динамическим диапазоном (HDR)

Разработка игрового содержимого с использованием всех цветовых возможностей HDR

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Общие сведения об HDR и цветовых концепциях (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/Build/2017/P4061">Освещение в HDR и расширенные цвета в DirectX</a></td>
    </tr>
    <tr>
        <td>Отображение HDR-содержимого и определение того, поддерживает ли его текущий дисплей</td>
        <td><a href="https://github.com/Microsoft/DirectX-Graphics-Samples/tree/master/Samples/UWP/D3D12HDR">Пример HDR</a></td>
    </tr>
    <tr>
        <td>Создание и настройка расширенного цвета с использованием DirectX</td>
        <td><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/D2DAdvancedColorImages">Пример отрисовки изображения Direct2D с расширенными цветами</a></td>
    </tr>   
</table>


### <a name="globalization-and-localization"></a>Глобализация и локализация

Создавайте игры для всего мира на платформе Windows и узнайте о возможностях интернационализации, встроенных в основные продукты Microsoft.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Подготовка игры для мирового рынка</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt186453.aspx">Руководство по разработке для глобальной аудитории</a></td>
    </tr>
    <tr>
        <td>Объединение языков, культур и технологий</td>
        <td><a href="https://www.microsoft.com/Language/Default.aspx">Интернет-ресурс с языковыми соглашениями и стандартной терминологией Майкрософт</a></td>
    </tr>
</table>

## <a name="submitting-and-publishing-your-game"></a>Отправка и публикация игры

Следующие руководства и сведения призваны максимально облегчить процесс публикации и отправки.

### <a name="publishing"></a>Публикация

Вы будете использовать [Центр партнеров](https://partner.microsoft.com/dashboard) для публикации и управления пакетами игры.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Публикация приложения центра партнеров</td>
        <td><a href="https://dev.windows.com/publish">Публикация приложений для Windows</a></td>
    </tr>
    <tr>
        <td>Расширенная публикация (GDN) с помощью центра партнеров</td>
        <td><a href="https://developer.xboxlive.com/en-us/windows/documentation/Pages/home.aspx">Руководство по расширенной публикации центр партнеров</a></td>
    </tr>
    <tr>
        <td>Использование Azure Active Directory (AAD) для добавления пользователей в вашей учетной записи центра партнеров</td>
        <td><a href="https://docs.microsoft.com/windows/uwp/publish/manage-account-users">Управление пользователями учетных записей</a></td>
    </tr>   
    <tr>
        <td>Оценка игры (запись блога)</td>
        <td><a href="https://blogs.windows.com/buildingapps/2016/01/06/now-available-single-age-rating-system-to-simplify-app-submissions/">Единый процесс для присвоения возрастных категорий с помощью системы IARC</a></td>
    </tr>
</table>

#### <a name="packaging-and-uploading"></a>Создание пакета и передача

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Научитесь использовать потоковую установку и дополнительные пакеты (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/Build/2017/B8093">Распространение приложений UWP следующего поколения: создание расширяемый, поток произвести componentizedapps</a></td>
    </tr>
    <tr>
        <td>Разделение и группировка содержимого для потоковой установки</td>
        <td><a href="../packaging/streaming-install.md">Потоковая установка приложений UWP</a></td>
    </tr>
    <tr>
        <td>Создание дополнительных пакетов, например DLC для игр</td>
        <td><a href="../packaging/optional-packages.md">Разработка дополнительных пакетов и связанных наборов</a></td>
    </tr>
    <tr>
        <td>Создание пакета игры UWP</td>
        <td><a href="../packaging/index.md">Создание пакетов приложений</a></td>
    </tr>
    <tr>
        <td>Создание пакета игры на базе DirectX для UWP</td>
        <td><a href="package-your-windows-store-directx-game.md">Создание пакета игры на базе DirectX для UWP</a></td>
    </tr>
    <tr>
        <td>Создание пакета игры в качестве стороннего разработчика (запись блога)</td>
        <td><a href="https://blogs.windows.com/buildingapps/2015/12/15/building-an-app-for-a-3rd-party-how-to-package-their-store-app/">Создание пакетов без доступа к учетной записи магазина издателя</a></td>
    </tr>
    <tr>
        <td>Создание пакетов приложений и их наборов с помощью MakeAppx</td>
        <td><a href="https://docs.microsoft.com/windows/uwp/packaging/create-app-package-with-makeappx-tool">Создание пакетов с помощью упаковщика MakeAppx.exe</a></td>
    </tr>
    <tr>
        <td>Цифровая подпись файлов с помощью SignTool</td>
        <td><a href="https://msdn.microsoft.com/library/windows/desktop/aa387764">Подпись файлов и проверка подписи с помощью SignTool</a></td>
    </tr>    
    <tr>
        <td>Отправка игры и управление версиями</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt148542">Отправка пакетов приложений</a></td>
    </tr>
</table>


### <a name="policies-and-certification"></a>Политики и сертификация

Не допускайте задержку выпуска игры из-за проблем с сертификацией. Предлагаем вашему вниманию информацию о политиках и распространенных проблемах сертификации.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Соглашение с разработчиком приложения Microsoft Store</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/hh694058">Соглашение с разработчиком приложения</a></td>
    </tr>
    <tr>
        <td>Правила публикации приложений в Microsoft Store</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/dn764944">Политики Microsoft Store</a></td>
    </tr>
    <tr>
        <td>Как избежать некоторых распространенных проблем с сертификацией приложений</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/jj657968">Недопущение распространенных ошибок при сертификации</a></td>
    </tr>
</table>
 

### <a name="store-manifest-storemanifestxml"></a>Манифест Магазина (StoreManifest.xml)

Манифест Магазина (StoreManifest.xml) — это необязательный файл конфигурации, который может быть включен в пакет приложения. Манифест магазина предоставляет дополнительные функции, не являющиеся частью файла AppxManifest.xml. Например, вы можете использовать манифест магазина, чтобы блокировать установку игры, если целевое устройство не имеет указанного минимума функций DirectX или указанной минимальной системной памяти.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Схема манифеста Магазина</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt617335">Схема манифеста Магазина (Windows 10)</a></td>
    </tr>
</table>
 

## <a name="game-lifecycle-management"></a>Управление жизненным циклом игры


Завершив создание игры и отправив ее, не считайте, что вы все сделали. Возможно, вы завершили разработку первой версии, однако путешествие вашей игры на рынке только начинается. Вам будет необходимо следить за пользованием и получать отчеты об ошибках, реагировать на отзывы пользователей и публиковать обновления игры.

### <a name="partner-center-analytics-and-promotion"></a>Аналитика центра партнеров и продвижения

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Аналитика центра партнеров</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt148522">Анализ эффективности приложений</a></td>
    </tr>
    <tr>
        <td>Получение информации о том, как клиенты взаимодействуют с функциями Xbox в вашей игре</td>
        <td><a href="../publish/xbox-analytics-report.md">Аналитический отчет Xbox</a></td>
    </tr>
    <tr>
        <td>Ответ на отзывы клиентов</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt148546">Реакция на отзывы пользователей</a></td>
    </tr>
    <tr>
        <td>Способы продвижения игры</td>
        <td><a href="https://dev.windows.com/store-promotion">Продвижение приложений</a></td>
    </tr>
</table>
 

### <a name="visual-studio-application-insights"></a>Visual Studio Application Insights

Статистика Visual Studio Application Insights позволяет получать аналитические сведения о производительности, телеметрии и использовании опубликованной игры. Статистика Application Insights помогает обнаруживать и устранять проблемы после выхода игры, постоянно контролировать и совершенствовать игровой процесс, понимать характер взаимодействия игроков с игрой. Статистика Application Insights работает путем добавления SDK в приложение, которое отправляет телеметрию на [портал Azure](https://portal.azure.com/).

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Аналитика производительности и использования приложения</td>
        <td><a href="https://azure.microsoft.com/documentation/articles/app-insights-get-started/">Visual Studio Application Insights</a></td>
    </tr>
    <tr>
        <td>Включение статистики Application Insights в приложениях для Windows</td>
        <td><a href="https://azure.microsoft.com/documentation/articles/app-insights-windows-get-started/">Статистика Application Insights для приложений для Windows Phone и Магазина</a></td>
    </tr>
</table>


### <a name="third-party-solutions-for-analytics-and-promotion"></a>Сторонние решения для анализа и продвижения

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Анализ поведения игроков с помощью GameAnalytics</td>
        <td><a href="https://www.gameanalytics.com/">GameAnalytics</a></td>
    </tr>
    <tr>
        <td>Подключение игры UWP к Google Analytics</td>
        <td><a href="https://github.com/dotnet/windows-sdk-for-google-analytics">Получить Windows SDK для Google Analytics</a></td>
    </tr>
    <tr>
        <td>Узнайте, как использовать Windows SDK для Google Analytics (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Creators-Update/Getting-started-with-the-Windows-SDK-for-Google-Analytics">Начало работы с Windows SDK для Google Analytics</a></td>
    </tr>    
    <tr>
        <td>Использование рекламных объявлений об установке в приложении Facebook для продвижения игры среди пользователей Facebook</td>
        <td><a href="https://github.com/Microsoft/winsdkfb">Получить Windows SDK для Facebook</a></td>
    </tr>
    <tr>
        <td>Узнайте, как использовать рекламные объявления об установке в приложении Facebook (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Creators-Update/Getting-started-with-Facebook-App-Install-Ads">Начало работы с Windows SDK для Facebook</a></td>
    </tr>
    <tr>
        <td>Использование Vungle для добавления видеорекламы в игры</td>
        <td><a href="https://v.vungle.com/sdk">Получить Windows SDK для Vungle</a></td>
    </tr>
</table>
 

### <a name="creating-and-managing-content-updates"></a>Создание обновлений содержимого и управление ими

Чтобы обновить опубликованную игру, отправьте новый пакет приложения с более высоким номером версии. После завершения процесса отправки и сертификации пакета он станет автоматически доступен пользователям как обновление.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Обновление и управление версиями игры</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt188602">Нумерация версий пакета</a></td>
    </tr>
    <tr>
        <td>Руководство по управлению пакетами игры</td>
        <td><a href="https://msdn.microsoft.com/library/windows/apps/mt188602">Руководство по управлению пакетами приложения</a></td>
    </tr>
</table>


## <a name="adding-xbox-live-to-your-game"></a>Добавление Xbox Live в игру

Xbox Live — это высококлассная игровая сеть, которая объединяет миллионы игроков по всему миру. Разработчики получают доступ к функциям Xbox Live, способным обеспечить органический рост аудитории игры — например, присутствию на Xbox Live, спискам лидеров, облачным сохранениям, центрам игр, клубам, командным чатам, DVR для игр, и др.

> [!Note]
> Если вы хотите создавать игры с поддержкой Xbox Live, вам доступно несколько вариантов. Сведения о различных приложений см. в разделе [Обзор программ для разработчиков](../xbox-live/developer-program-overview.md).

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Обзор Xbox Live</td>
        <td><a href="../xbox-live/index.md">Руководство по Xbox Live для разработчиков</a></td>
    </tr>
    <tr>
        <td>Описание функций, доступных в разных программах</td>
        <td><a href="../xbox-live/developer-program-overview.md#feature-table">Обзор программ для разработчиков: таблица функций</a></td>
    </tr>
    <tr>
        <td>Ссылки на полезные ресурсы для разработки игр для Xbox Live</td>
        <td><a href="../xbox-live/xbox-live-resources.md">Ресурсы по Xbox Live</a></td>
    </tr>
    <tr>
        <td>Получение данных от служб Xbox Live</td>
        <td><a href="../xbox-live/introduction-to-xbox-live-apis.md">Введение в API-интерфейсы Xbox Live</a></td>
    </tr>
</table>


### <a name="for-developers-in-the-xbox-live-creators-program"></a>Для разработчиков-участников программы Xbox Live Creators Program

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Обзор</td>
        <td><a href="../xbox-live/get-started-with-creators/get-started-with-xbox-live-creators.md">Начало работы с программой Xbox Live Creators Program</a></td>
    </tr>
    <tr>
        <td>Добавление Xbox Live в игру</td>
        <td><a href="../xbox-live/get-started-with-creators/creators-step-by-step-guide.md">Пошаговое руководство по интеграции с программой Xbox Live Creators Program</a></td>
    </tr>
    <tr>
        <td>Добавление Xbox Live в игру UWP, созданную с помощью Unity</td>
        <td><a href="../xbox-live/get-started-with-creators/develop-creators-title-with-unity.md">Начало разработки игры в рамках программы Xbox Live Creators Program с помощью игрового движка Unity</a></td>
    </tr>
    <tr>
        <td>Создание песочницы для разработки</td>
        <td><a href="../xbox-live/get-started-with-creators/xbox-live-sandboxes-creators.md">Основные сведения о песочницах Xbox Live</a></td>
    </tr>
    <tr>
        <td>Настройка учетных записей для тестирования</td>
        <td><a href="../xbox-live/get-started-with-creators/authorize-xbox-live-accounts.md">Авторизация учетных записей Xbox Live в тестовой среде</a></td>
    </tr>
    <tr>
        <td>Примеры для Xbox Live Creators Program</td>
        <td><a href="https://github.com/Microsoft/xbox-live-samples/tree/master/Samples/CreatorsSDK">Примеры кода для разработчиков, участвующих в Creators Program</a></td>
    </tr>
    <tr>
        <td>Интеграция межплатформенных возможностей Xbox Live в игры UWP (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2017/GDC2017-005">Программа Xbox Live Creators Program</a></td>
    </tr>  
</table>

### <a name="for-managed-partners-and-developers-in-the-idxbox-program"></a>Для управляемых партнеров и разработчиков в программе ID@Xbox

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Обзор</td>
        <td><a href="../xbox-live/get-started-with-partner/get-started-with-xbox-live-partner.md">Начало работы с Xbox Live в качестве управляемого партнера или разработчика ID</a></td>
    </tr>
    <tr>
        <td>Добавление Xbox Live в игру</td>
        <td><a href="../xbox-live/get-started-with-partner/partners-step-by-step-guide.md">Пошаговое руководство по интеграции Xbox Live для управляемых партнеров и членов ID</a></td>
    </tr>
    <tr>
        <td>Добавление Xbox Live в игру UWP, созданной с помощью Unity</td>
        <td><a href="../xbox-live/get-started-with-partner/partner-unity-uwp-il2cpp.md">Добавление поддержки Xbox Live в Unity для UWP с использованием сервера сценариев IL2CPP для участников с ID и управляемых партнеров</a></td>
    </tr>
    <tr>
        <td>Создание песочницы для разработки</td>
        <td><a href="../xbox-live/get-started-with-partner/advanced-xbox-live-sandboxes.md">Расширенные сведения о песочницах Xbox Live</a></td>
    </tr>
    <tr>
        <td>Требования к играм, использующим Xbox Live (GDN)</td>
        <td><a href="https://go.microsoft.com/fwlink/?LinkId=533217">Требования к Xbox для Xbox Live в Windows 10</a></td>
    </tr>
    <tr>
        <td>Примеры</td>
        <td><a href="https://github.com/Microsoft/xbox-live-samples/tree/master/Samples/ID%40XboxSDK">Примеры кода для разработчиков ID@Xbox</a></td>
    </tr>  
    <tr>
        <td>Обзор разработки игр для Xbox Live (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/Developing-with-Xbox-Live-for-Windows-10">Разработка с использованием Xbox Live для Windows 10</a></td>
    </tr>
    <tr>
        <td>Кроссплатформенное проведение матчей (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/Xbox-Live-Multiplayer-Introducing-services-for-cross-platform-matchmaking-and-gameplay">Многопользовательский режим Xbox Live: знакомство со службой кросс-платформенного проведения матчей и игр</a></td>
    </tr>
    <tr>
        <td>Многопользовательские игры на разных устройствах в Fable Legends (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/Fable-Legends-Cross-device-Gameplay-with-Xbox-Live">Fable Legends: многопользовательские игры на разных устройствах с Xbox Live</a></td>
    </tr>
    <tr>
        <td>Статистика и достижения Xbox Live (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/Best-Practices-for-Leveraging-Cloud-Based-User-Stats-and-Achievements-in-Xbox-Live">Рекомендации по использованию облачной статистики и достижений пользователя в Xbox Live</a></td>
    </tr>
</table>


## <a name="additional-resources"></a>Дополнительные ресурсы

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Видеоролики о разработке игр</td>
        <td><a href="https://docs.microsoft.com/windows/uwp/gaming/game-development-videos">Видео с больших конференций, таких как GDC и //build</a></td>
    </tr>
    <tr>
        <td>Независимая разработка игр (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/New-Opportunities-for-Independent-Developers">Новые возможности для независимых разработчиков</a></td>
    </tr>
    <tr>
        <td>Особенности многоядерных мобильных устройств (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/Sustained-gaming-performance-in-multi-core-mobile-devices">Стабильная производительность игры на многоядерных мобильных устройствах</a></td>
    </tr>
    <tr>
        <td>Разработка классических игр для Windows 10 (видео)</td>
        <td><a href="https://channel9.msdn.com/Events/GDC/GDC-2015/PC-Games-for-Windows-10">Компьютерные игры для Windows 10</a></td>
    </tr>
</table>



 

 

 
