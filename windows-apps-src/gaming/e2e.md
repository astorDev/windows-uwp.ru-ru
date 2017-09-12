---
author: joannaleecy
title: "Руководство по разработке игр для Windows10"
description: "Комплексное руководство по ресурсам и сведения о разработке игр для универсальной платформы Windows (UWP)."
ms.assetid: 6061F498-96A8-44EF-9711-68AE5A1218C9
ms.author: joanlee
ms.date: 08/03/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, игры, разработка игр"
ms.openlocfilehash: f84f33f4e30391624ae8d2615cb9c27442e168fb
ms.sourcegitcommit: 63c815f8c6665872987b5410cabf324f2b7e3c7c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2017
---
# <a name="windows-10-game-development-guide"></a>Руководство по разработке игр для Windows 10


Предлагаем вашему вниманию руководство по разработке игр для Windows 10.

Это руководство содержит полную коллекцию ресурсов и сведений, необходимых для разработки игры для универсальной платформы Windows (UWP). Версия этого руководства на английском языке (США) доступна в формате [PDF](http://download.microsoft.com/download/3/E/8/3E8F6376-D239-41A3-989C-DA1494C0024D/Windev_Game_Dev_Guide_May_2017.pdf).

## <a name="introduction-to-game-development-for-the-universal-windows-platform-uwp"></a>Введение в разработку игр для универсальной платформы Windows (UWP)


При создании игры для Windows10 вам предоставляется возможность выйти на многомиллионную аудиторию игроков по всему миру на телефонах, компьютерах и Xbox One. Благодаря службе Xbox в Windows, Xbox Live, возможности многопользовательской игры на разных устройствах, замечательному игровому сообществу и мощным новым функциям, таким как универсальная платформа Windows (UWP) и DirectX 12, игры для Windows10 самых разных жанров привлекают игроков всех возрастов. Новая универсальная платформа Windows (UWP) обеспечивает совместимость игры на разных устройствах Windows 10 с помощью общего API для телефонов, компьютеров и Xbox One, а также средств и возможностей для адаптации игры к возможностям каждого устройства.

Это руководство содержит полную коллекцию ресурсов и сведений, которые помогут вам в разработке игры. Разделы упорядочены по этапам разработки игры, поэтому вы будете знать, где находится нужная информация, когда она потребуется.

Для начала в разделе [Ресурсы по разработке игр](#game-development-resources) представлен общий обзор документации, программ и других ресурсов, полезных при создании игр.

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
        <td>[Центр разработки для Windows](https://dev.windows.com)</td>
    </tr>
    <tr>
        <td>Разработка приложений для Windows</td>
        <td>[Разработка приложений для Windows](https://dev.windows.com/develop)</td>
    </tr>
    <tr>
        <td>Разработка приложений универсальной платформы Windows</td>
        <td>[Инструкции к приложениям для Windows 10](https://msdn.microsoft.com/library/windows/apps/mt244352)</td>
    </tr>
    <tr>
        <td>Инструкции к играм для UWP</td>
        <td>[Игры и DirectX](index.md) </td>
    </tr>
    <tr>
        <td>Справочник и обзоры по DirectX</td>
        <td>[Графика и игры на основе DirectX](https://msdn.microsoft.com/library/windows/desktop/ee663274)</td>
    </tr>
    <tr>
        <td>Azure для игр</td>
        <td>[Создание и масштабирования игр с помощью Azure](https://azure.microsoft.com/solutions/gaming/)</td>
    </tr>
    <tr>
        <td>Приложения UWP для Xbox One</td>
        <td>[Создание приложений UWP для Xbox One](https://msdn.microsoft.com/windows/uwp/xbox-apps/index)</td>
    </tr>
    <tr>
        <td>UWP для HoloLens</td>
        <td>[Создание приложений UWP для HoloLens](https://developer.microsoft.com/windows/mixed-reality/development_overview)</td>
    </tr>
    <tr>
        <td>Документация по Xbox Live</td>
        <td>[Руководство по Xbox Live для разработчиков](../xbox-live/index.md)</td>
    </tr>
    <tr>
        <td>Документация для разработчиков по Xbox One (GDN)</td>
        <td>[Документация по Xbox One XDK](https://developer.xboxlive.com/en-us/platform/development/documentation/Pages/home.aspx)</td>
    </tr>
    <tr>
        <td>Технические документы для разработчиков по Xbox One (GDN)</td>
        <td>[Технические документы](https://developer.xboxlive.com/en-us/platform/development/education/Pages/WhitePapers.aspx)</td>
    </tr>
    <tr>
        <td>Интерактивная документация по Mixer</td>
        <td>[Добавление интерактивных возможностей в игру](https://dev.mixer.com/reference/interactive/index.html)</td>
    </tr>        
</table>

### <a name="windows-dev-center"></a>Центр разработки для Windows

Регистрация учетной записи разработчика в Центре разработки для Windows — первый шаг к публикации игры для Windows. Учетная запись разработчика позволит вам резервировать название для своей игры и отправлять в Магазин Windows бесплатные и платные игры для любых устройств с Windows. С помощью учетной записи разработчика вы сможете управлять своими играми и внутренними продуктами приложений, получать подробную аналитику и включать службы для предоставления замечательных возможностей вашим игрокам по всему миру. 

Корпорация Майкрософт также предлагает несколько программ для разработчиков, призванных помочь вам разрабатывать и публиковать игры для Windows. Рекомендуется проверить, подходят ли они вам, перед регистрацией учетной записи Центра разработки. Подробнее см. в разделе [Программы для разработчиков](#developer-programs).

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Зарегистрировать учетную запись разработчика</td>
        <td>[Вы готовы зарегистрироваться?](https://msdn.microsoft.com/library/windows/apps/bg124287)</td>
    </tr> 
</table>

### <a name="developer-programs"></a>Программы для разработчиков

Корпорация Майкрософт предлагает несколько программ для разработчиков, призванных помочь вам разрабатывать и публиковать игры для Windows. Рассмотрите возможность присоединения к программе для разработчиков, если вы хотите создавать игры для Xbox One и использовать в них функции Xbox Live. Чтобы опубликовать игру в Магазине Windows, вам также понадобится создать учетную запись разработчика в Центре разработки для Windows. 

#### <a name="idxbox"></a>ID@Xbox

Программа ID@Xbox помогает квалифицированных разработчикам самостоятельно публиковать свои игры для Windows и Xbox One. Если вы хотите создать игру для Xbox One или добавить в свою игру для Windows 10 функциональные возможности Xbox Live, например счет игрока, достижения или списки лидеров, зарегистрируйтесь в ID@Xbox прямо сейчас. Став разработчиком ID@Xbox, вы получите необходимые средства и поддержку для успешного воплощения своих творческих идей. Перед регистрацией учетной записи разработчика в Центре разработки для Windows рекомендуется подать заявку на участие в программе ID@Xbox.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Программа для разработчиков ID@Xbox</td>
        <td>[Программа независимых разработчиков для Xbox One](http://go.microsoft.com/fwlink/p/?LinkID=526271)</td>
    </tr>
    <tr>
        <td>Сайт для потребителей ID@Xbox</td>
        <td>[ID@Xbox](http://www.idatxbox.com/)</td>
    </tr>
</table>

#### <a name="xbox-live-creators-program"></a>Программа Xbox Live Creators Program

Программа Xbox Live Creators Program позволяет любому желающему интегрировать Xbox Live в свою игру и опубликовать ее для Xbox One и Windows 10. Чтобы приступить к разработке с использованием программы Xbox Live Creators Program, зарегистрируйтесь сегодня.

Если вы хотите получить доступ к еще большим возможностям Xbox Live, находиться в основном магазине Xbox One или получать специальную маркетинговую поддержку и поддержку при разработке, примите участие в программе [ID@Xbox](http://www.xbox.com/Developers/id).

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Программа Xbox Live Creators Program</td>
        <td>[Интеграция функций Xbox Live в вашей игре](https://developer.microsoft.com/games/xbox/xboxlive/creator)</td>
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
        <td>[Windows-universal-samples](https://github.com/Microsoft/Windows-universal-samples)</td>
    </tr>
    <tr>
        <td>Открытые примеры, опубликованные группой Xbox Advanced Technology Group</td>
        <td>[Xbox-ATG-Samples](https://github.com/Microsoft/Xbox-ATG-Samples)</td>
    </tr>
    <tr>
        <td>Примеры графики Direct3D 12</td>
        <td>[DirectX-Graphics-Samples](https://github.com/Microsoft/DirectX-Graphics-Samples)</td>
    </tr>
    <tr>
        <td>Примеры графики Direct3D 11</td>
        <td>[directx-sdk-samples](https://github.com/walbourn/directx-sdk-samples)</td>
    </tr>
    <tr>
        <td>Пример игры Direct3D 11 от первого лица</td>
        <td>[Создание простой игры UWP с использованием DirectX](tutorial--create-your-first-metro-style-directx-game.md)</td>
    </tr>
    <tr>
        <td>Пример эффектов пользовательского изображения Direct2D</td>
        <td>[D2DCustomEffects](http://go.microsoft.com/fwlink/p/?LinkId=620531)</td>
    </tr>
    <tr>
        <td>Пример сетки градиента Direct2D</td>
        <td>[D2DGradientMesh](http://go.microsoft.com/fwlink/p/?LinkId=620532)</td>
    </tr>
    <tr>
        <td>Пример корректировки фотографии Direct2D</td>
        <td>[D2DPhotoAdjustment](http://go.microsoft.com/fwlink/p/?LinkId=620533)</td>
    </tr>
    <tr>
        <td>Примеры игр для Xbox One (GDN)</td>
        <td>[Примеры](https://developer.xboxlive.com/en-us/platform/development/education/Pages/Samples.aspx)</td>
    </tr>
    <tr>
        <td>Примеры игр для Windows 8 (Коллекция исходных кодов MSDN)</td>
        <td>[Примеры игр из Магазина Windows](https://code.msdn.microsoft.com/windowsapps/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=game&f%5B1%5D.Type=Contributors&f%5B1%5D.Value=Microsoft&f%5B1%5D.Text=Microsoft)</td>
    </tr>
    <tr>
        <td>Пример игры с использованием JavaScript и HTML5</td>
        <td>[Пример сенсорной игры с использованием JavaScript и HTML5](https://code.msdn.microsoft.com/windowsapps/JavaScript-and-HTML5-touch-d96f6031)</td>
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
        <td>Форумы разработчиков приложений для Windows</td>
        <td>[Форумы, посвященные приложениям и Магазину Windows](https://social.msdn.microsoft.com/Forums/en-us/home?category=windowsapps)</td>
    </tr>
    <tr>
        <td>Форум разработчиков приложений UWP</td>
        <td>[Разработка приложений универсальной платформы Windows](https://social.msdn.microsoft.com/Forums/en-us/home?forum=wpdevelop)</td>
    </tr>

    <tr>
        <td>Форумы разработчиков классических приложений</td>
        <td>[Форумы, посвященные классическим приложениям для Windows](https://social.msdn.microsoft.com/Forums/en-us/home?category=windowsdesktopdev)</td>
    </tr>
    <tr>
        <td>Игры Магазина Windows на базе DirectX (архив записей форума)</td>
        <td>[Создание игр Магазина Windows на базе DirectX (архив)](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=wingameswithdirectx)</td>
    </tr>
    <tr>
        <td>Форум разработчиков для управляемых партнеров Windows 10</td>
        <td>[Форумы разработчиков для XBOX: Windows 10](http://aka.ms/win10devforums)</td>
    </tr>
    <tr>
        <td>Форумы DirectX</td>
        <td>[Форум DirectX 12](http://forums.directxtech.com/index.php)</td>
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
        <td>[Создание приложений для Windows](http://blogs.windows.com/buildingapps/)</td>
    </tr>
    <tr>
        <td>Windows 10 (записи в блоге)</td>
        <td>[Записи в блоге Windows 10](http://blogs.windows.com/blog/tag/windows-10/)</td>
    </tr>
    <tr>
        <td>Блог группы разработчиков Visual Studio</td>
        <td>[Блог Visual Studio](http://blogs.msdn.com/b/visualstudio/)</td>
    </tr>
    <tr>
        <td>Блоги о средствах разработчика Visual Studio</td>
        <td>[Блоги о средствах разработчика](http://blogs.msdn.com/b/developer-tools/)</td>
    </tr>
    <tr>
        <td>Блог о средствах разработчика Somasegar</td>
        <td>[Блог Somasegar](http://blogs.msdn.com/b/somasegar/)</td>
    </tr>
    <tr>
        <td>Блог разработчиков DirectX</td>
        <td>[Блог разработчиков DirectX](http://blogs.msdn.com/b/directx)</td>
    </tr>
    <tr>
        <td>Введение в DirectX 12 (запись в блоге)</td>
        <td>[DirectX12](http://blogs.msdn.com/b/directx/archive/2014/03/20/directx-12.aspx)</td>
    </tr>
    <tr>
        <td>Блог группы разработчиков средств Visual C++</td>
        <td>[Блог рабочей группы Visual C++](http://blogs.msdn.com/b/vcblog/)</td>
    </tr>
    <tr>
        <td>Блог группы PIX</td>
        <td>[Оптимизация производительности и отладка игр на основе DirectX 12 для Windows и Xbox](https://blogs.msdn.microsoft.com/pix/)</td>
    </tr>
    <tr>
        <td>Блог группы развертывания универсальных приложений для Windows</td>
        <td>[Блог группы создания и развертывания приложений UWP](https://blogs.msdn.microsoft.com/appinstaller/)</td>
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
        <td>[Технологии игр для приложений UWP](game-development-platform-guide.md)</td>
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
        <td>[Разработка игр для Windows10](http://channel9.msdn.com/Events/GDC/GDC-2015/Developing-Games-for-Windows-10)</td>
    </tr>
    <tr>
        <td>Игровое взаимодействие в Windows 10 (видео)</td>
        <td>[Игровое взаимодействие с пользователем в Windows 10](http://channel9.msdn.com/Events/GDC/GDC-2015/Gaming-Consumer-Experience-on-Windows-10)</td>
    </tr>
    <tr>
        <td>Игры в экосистеме Microsoft (видео)</td>
        <td>[Будущее игр в экосистеме Microsoft](http://channel9.msdn.com/Events/GDC/GDC-2015/The-Future-of-Gaming-Across-the-Microsoft-Ecosystem)</td>
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
        <td>[Специальные возможности для игр](https://msdn.microsoft.com/windows/uwp/gaming/accessibility-for-games)</td>
    </tr>
    <tr>
        <td>Создание игр с помощью облака</td>
        <td>[Облако для игр](https://msdn.microsoft.com/windows/uwp/gaming/cloud-for-games)</td>
    </tr>
    <tr>
        <td>Монетизация игры</td>
        <td>[Монетизация для игр](https://msdn.microsoft.com/windows/uwp/gaming/monetization-for-games)</td>
    </tr>
</table>



### <a name="choosing-your-graphics-technology-and-programming-language"></a>Выбор графической технологии и языка программирования

Существуют несколько языков программирования и графических технологий, доступных для использования в играх для Windows 10. Избранный вариант зависит от типа разрабатывающейся игры, опыта и предпочтений студии разработки, а также специальных функциональных требований игры. Будете ли вы использовать C#, C++ или JavaScript? DirectX, XAML или HTML5?

#### <a name="directx"></a>DirectX

Microsoft DirectX — это технология, которую следует выбрать для двух- и трехмерной графики и файлов мультимедиа с наивысшей производительностью. 

Direct3D 12, новая возможность в Windows 10, обеспечивает мощность интерфейсов API, работающих по типу консоли, а также беспрецедентные эффективность и быстродействие. Ваша игра может полностью использовать современное графическое оборудование и насчитывать больше объектов, обладать более детальными декорациями, содержать более впечатляющие эффекты. Direct3D12 обеспечивает оптимизированную графику на компьютерах с Windows10 и консоли XboxOne. Если вы хотите использовать знакомый графический конвейер Direct3D 11, вы все равно сможете воспользоваться преимуществами новых функций рендеринга и оптимизации, добавленных в Direct3D 11.3. А если вы проверенный временем разработчик классического API Windows, начавший работу с Win32, вы по-прежнему будете иметь эту возможность в Windows 10.

Исчерпывающие функции и глубокая интеграция платформы DirectX обеспечивают мощность и производительность для наиболее требовательных игр.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Инструкции для игр DirectX</td>
        <td>[Игры и DirectX](index.md)</td>
    </tr>
    <tr>
        <td>Обзоры и справочник по DirectX</td>
        <td>[Графика и игры на основе DirectX](https://msdn.microsoft.com/library/windows/desktop/ee663274)</td>
    </tr>
    <tr>
        <td>Справочник и руководство по программированию для Direct3D12</td>
        <td>[Графика Direct3D 12](https://msdn.microsoft.com/library/windows/desktop/dn903821)</td>
    </tr>
    <tr>
        <td>Видео о разработке графики и DirectX 12 (канал YouTube)</td>
        <td>[Обучение Microsoft DirectX 12 и графике](https://www.youtube.com/channel/UCiaX2B8XiXR70jaN7NK-FpA)</td>
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
        <td>[Платформа XAML](https://msdn.microsoft.com/library/windows/apps/mt228259)</td>
    </tr>
    <tr>
        <td>Пользовательский интерфейс и элементы управления XAML</td>
        <td>[Элементы управления, макеты и текст](https://msdn.microsoft.com/library/windows/apps/mt228348)</td>
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
        <td>[Справочник по HTML и модели DOM](https://msdn.microsoft.com/library/windows/apps/br212882.aspx)</td>
    </tr>
    <tr>
        <td>Рекомендации W3C по HTML5</td>
        <td>[HTML5](http://go.microsoft.com/fwlink/p/?linkid=221374)</td>
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
        <td>[DXGI](https://msdn.microsoft.com/library/windows/desktop/hh404534)</td>
    </tr>
    <tr>
        <td>Сочетание DirectX и XAML</td>
        <td>[Межпрограммное взаимодействие DirectX и XAML](directx-and-xaml-interop.md)</td>
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
        <td>[Справочник по языку Visual C++ (C++/CX)](https://msdn.microsoft.com/library/windows/apps/hh699871.aspx)</td>
    </tr>
    <tr>
        <td>Справочник и руководство по программированию для Visual C++</td>
        <td>[Visual C++ в Visual Studio 2015](https://msdn.microsoft.com/library/60k1461a.aspx)</td>
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
        <td>[Справочник по языку C#](https://msdn.microsoft.com/library/kx37x362.aspx)</td>
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
        <td>[Справка по JavaScript](https://msdn.microsoft.com/library/windows/apps/jj613794)</td>
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
        <td>[Создание компонентов среды выполнения Windows](https://docs.microsoft.com/windows/uwp/winrt-components/creating-windows-runtime-components-in-cpp)</td>
    </tr>
</table>


### <a name="which-version-of-directx-should-your-game-use"></a>Какая версия DirectX должна использоваться в вашей игре?

При использовании DirectX для игры необходимо выбрать версию, которая будет использоваться: Microsoft Direct3D12 или Microsoft Direct3D11.

Direct3D 12, новая возможность в Windows 10, обеспечивает мощь интерфейсов API, работающих по типу консоли, а также беспрецедентные эффективность и быстродействие. Ваша игра может полностью использовать современное графическое оборудование и насчитывать больше объектов, обладать более детальными декорациями, содержать более впечатляющие эффекты. Direct3D12 обеспечивает оптимизированную графику на компьютерах с Windows10 и консоли XboxOne. Так как Direct3D 12 работает на очень низком уровне, с его помощью группа профессиональных разработчиков графики или DirectX 11 может получить все необходимое для максимальной оптимизации графики.

Direct3D 11.3— это низкоуровневый графический API, использующий знакомую модель программирования Direct3D. Он берет на себя больше сложных операций, связанных с отрисовкой GPU. Он также поддерживается в Windows 10 и на Xbox One. Если у вас уже есть графическая подсистема на базе Direct3D 11 и вы еще не готовы перейти на Direct3D 12, вы можете использовать Direct3D 11 на 12, чтобы добиться частичного повышения производительности. Версии от 11.3 и выше включают новые функции отрисовки и оптимизации, которые также доступны в Direct3D 12.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Выбор между Direct3D 12 и Direct3D 11</td>
        <td>[Что такое Direct3D12?](https://msdn.microsoft.com/library/windows/desktop/dn899228)</td>
    </tr>
    <tr>
        <td>Обзор Direct3D 11</td>
        <td>[Графика Direct3D 11](https://msdn.microsoft.com/library/windows/desktop/ff476080)</td>
    </tr>
    <tr>
        <td>Обзор Direct3D 11 на 12</td>
        <td>[Direct3D 11 на 12](https://msdn.microsoft.com/library/windows/desktop/dn913195)</td>
    </tr>
</table>


### <a name="bridges-game-engines-and-middleware"></a>Мосты, игровые движки и ПО промежуточного слоя

В зависимости от потребностей вашей игры, использование мостов, игровых движков или ПО промежуточного слоя может сэкономить время и ресурсы, затрачиваемые на разработку и тестирование. Предлагаем вашему вниманию некоторые обзоры и ресурсы по мостам, игровым движкам и ПО промежуточного слоя, которые помогут вам решить, подходят ли они вам.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Разработка игр с помощью ПО промежуточного слоя (видео)</td>
        <td>[Ускорение разработки игр Магазина Windows с помощью ПО промежуточного слоя](https://channel9.msdn.com/Events/Build/2013/3-187)</td>
    </tr>
    <tr>
        <td>Введение в ПО промежуточного слоя (запись в блоге)</td>
        <td>[ПО промежуточного слоя для разработки игр — что это такое? Необходимо ли оно мне?](http://blogs.msdn.com/b/wsdevsol/archive/2014/05/02/game-development-middleware-what-is-it-do-i-need-it.aspx)</td>
    </tr>
</table>
 

#### <a name="universal-windows-platform-bridges"></a>Мосты универсальной платформы Windows

Мосты универсальной платформы Windows — это технологии, которые делают имеющиеся приложение или игру доступными на UWP. Мосты — это замечательный способ быстро начать разработку игр для UWP.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Мосты UWP</td>
        <td>[Используйте свой код при разработке для Windows](https://dev.windows.com/bridges/)</td>
    </tr>
    <tr>
        <td>Мост Windows для iOS</td>
        <td>[Перенос приложений для iOS в Windows](https://dev.windows.com/bridges/ios)</td>
    </tr>
    <tr>
        <td>Мост Windows для классических приложений (.NET и Win32)</td>
        <td>[Преобразование классического приложения в приложение UWP](https://developer.microsoft.com/windows/bridges/desktop)</td>
    </tr>
</table>
 

#### <a name="unity"></a>Unity

Unity 5 — это следующее поколение удостоенной наград платформы для разработки двух- и трехмерных игр и интерактивных элементов. Unity5 предлагает новые художественные инструменты, расширенные графические возможности и повышенную эффективность.

Платформа Unity поддерживает разработку в Direct3D 12 (начиная с версии Unity 5.4).

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Игровая подсистема Unity</td>
        <td>[Unity — игровой движок](http://unity3d.com/)</td>
    </tr>
    <tr>
        <td>Получить Unity 5</td>
        <td>[Получить Unity](http://unity3d.com/get-unity)</td>
    </tr>
    <tr>
        <td>Поддержка приложений универсальной платформы Windows в Unity 5.2 (запись в блоге)</td>
        <td>[Приложения универсальной платформы для Windows 10 в Unity 5.2](http://blogs.unity3d.com/2015/09/09/windows-10-universal-apps-in-unity-5-2/)</td>
    </tr>
    <tr>
        <td>Документация Unity для Windows</td>
        <td>[Руководство по Unity для Windows](http://docs.unity3d.com/Manual/Windows.html)</td>
    </tr>
    <tr>
        <td>Добавление интерактивных возможностей в игру с помощью Mixer Interactive</td>
        <td>[Руководство по началу работы](https://github.com/mixer/interactive-unity-plugin/wiki/Getting-started)</td>
    </tr>
    <tr>
        <td>Пакет Mixer SDK для Unity</td>
        <td>[Подключаемый модуль Mixer Unity](https://www.assetstore.unity3d.com/en/#!/content/88585)</td>
    </tr>
    <tr>
        <td>Справочная документация по Mixer SDK для Unity</td>
        <td>[Справочник по API для подключаемого модуля Mixer Unity](https://dev.mixer.com/reference/interactive/csharp/index.html)</td>
    </tr>
    <tr>
        <td>Публикация игры Unity в Магазине Windows</td>
        <td>[Руководство по портированию](https://unity3d.com/partners/microsoft/porting-guides)</td>
    </tr>
    <tr>
        <td>Публикация игры Unity как приложения универсальной платформы Windows (видео)</td>
        <td>[Как опубликовать игру Unity в качестве приложения UWP](https://channel9.msdn.com/Blogs/One-Dev-Minute/How-to-publish-your-Unity-game-as-a-UWP-app)</td>
    </tr>
    <tr>
        <td>Использование Unity для создания игр и приложений для Windows (видео)</td>
        <td>[Создание игр и приложений для Windows с помощью Unity](https://channel9.msdn.com/Blogs/One-Dev-Minute/Making-games-and-apps-with-Unity)</td>
    </tr>
    <tr>
        <td>Разработка игры Unity с помощью Visual Studio (серия видео)</td>
        <td>[Использование Unity с Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=722359)</td>
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
        <td>[Havok](http://www.havok.com/)</td>
    </tr>
    <tr>
        <td>Набор средств Havok</td>
        <td>[Обзор продуктов Havok](http://www.havok.com/products/)</td>
    </tr>
    <tr>
        <td>Форумы технической поддержки Havok</td>
        <td>[Havok](http://support.havok.com)</td>
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
        <td>[Веб-сайт MonoGame](http://www.monogame.net)</td>
    </tr>
    <tr>
        <td>Документация по MonoGame</td>
        <td>[Документация MonoGame (последняя)](http://www.monogame.net/documentation/)</td>
    </tr>
    <tr>
        <td>Скачиваемые файлы Monogame</td>
        <td>[Скачайте выпуски, сборки для разработчиков и исходный код](http://www.monogame.net/downloads/) с веб-сайта MonoGame или [получите последний выпуск через NuGet](https://www.nuget.org/profiles/MonoGame).
    </tr>
</table>


#### <a name="cocos2d"></a>Cocos2d

Cocos2d-X — это кроссплатформенный набор средств и движков для разработки игр с открытым кодом, поддерживающий разработку игр UWP. С версии 3 добавлена функция 3D.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Cocos2d-x</td>
        <td>[Что такое Cocos2d-X?](http://www.cocos2d-x.org/)</td>
    </tr>
    <tr>
        <td>Руководство по Cocos2d-x для программистов</td>
        <td>[Руководство по Cocos2d-x для программистов (ред. 3.8)](http://www.cocos2d-x.org/programmersguide/)</td>
    </tr>
    <tr>
        <td>Cocos2d-x в Windows 10 (запись в блоге)</td>
        <td>[Запуск Cocos2d-x в Windows 10](https://blogs.windows.com/buildingapps/2015/06/15/running-cocos2d-x-on-windows-10/)</td>
    </tr>
    <tr>
        <td>Игры Магазина Windows на Cocos2d-x (видео)</td>
        <td>[Разработка игр на Cocos2d-x для устройств с Windows](http://www.microsoftvirtualacademy.com/training-courses/build-a-game-with-cocos2d-x-for-windows-devices)</td>
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
        <td>[Unreal Engine 4](https://www.unrealengine.com/what-is-unreal-engine-4)</td>
    </tr>
</table>

#### <a name="babylonjs"></a>BabylonJS

BabylonJS — это полная платформа JavaScript для создания трехмерных игр с помощью HTML5, WebGL и веб-аудио.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>BabylonJS</td>
        <td>[BabylonJS](http://www.babylonjs.com/)</td>
    </tr>
    <tr>
        <td>WebGL 3D с HTML5 и BabylonJS (серия видео)</td>
        <td>[Изучение WebGL 3D и BabylonJS](https://channel9.msdn.com/Series/Introduction-to-WebGL-3D-with-HTML5-and-Babylonjs/01)</td>
    </tr>
    <tr>
        <td>Создание кросс-платформенной игры WebGL с помощью BabylonJS</td>
        <td>[Использование BabylonJS для разработки кросс-платформенной игры](https://www.smashingmagazine.com/2016/07/babylon-js-building-sponza-a-cross-platform-webgl-game/)</td>
    </tr>    
</table>

### <a name="middleware-and-partners"></a>ПО промежуточного слоя и партнеры

У нас имеется большое количество партнеров по движкам и ПО промежуточного слоя, которые предлагают различные решения в зависимости от ваших потребностей при разработке игр.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Партнеры Центра разработки для Windows</td>
        <td>[Партнеры Центра разработки](https://developer.microsoft.com/windows/app-middleware-partners)</td>
    </tr>
</table>


### <a name="porting-your-game"></a>Перенос игры

Если у вас уже есть игра, существует множество ресурсов и руководств, которые помогут быстро сделать игру доступной на UWP. Для ускорения процесса переноса можно также попробовать использовать [Мост универсальной платформы Windows](#universal-windows-platform-bridges).

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Перенос приложения для Windows 8 на универсальную платформу Windows</td>
        <td>[Переход со среды выполнения Windows 8.x на UWP](https://msdn.microsoft.com/library/windows/apps/mt238322)</td>
    </tr>
    <tr>
        <td>Перенос приложения для Windows 8 на универсальную платформу Windows (видео)</td>
        <td>[Перенос приложений 8.1 в Windows 10](https://channel9.msdn.com/Series/A-Developers-Guide-to-Windows-10/21)</td>
    </tr>
    <tr>
        <td>Перенос приложения iOS на универсальную платформу Windows</td>
        <td>[Переход с iOS на UWP](https://msdn.microsoft.com/library/windows/apps/mt238320)</td>
    </tr>
    <tr>
        <td>Перенос приложения Silverlight на универсальную платформу Windows</td>
        <td>[Переход с WindowsPhoneSilverlight на UWP](https://msdn.microsoft.com/library/windows/apps/mt238323)</td>
    </tr>
    <tr>
        <td>Перенос приложения с XAML или Silverlight на универсальную платформу Windows (видео)</td>
        <td>[Перенос приложения из XAML или Silverlight в Windows 10](https://channel9.msdn.com/Events/Build/2015/3-741)</td>
    </tr>
    <tr>
        <td>Перенос игры для Xbox на универсальную платформу Windows</td>
        <td>[Перенос из Xbox One в UWP для Windows 10](https://developer.xboxlive.com/en-us/platform/development/education/Documents/Porting%20from%20Xbox%20One%20to%20Windows%2010.aspx)</td>
    </tr>
    <tr>
        <td>Перенос из DirectX 9 в DirectX 11</td>
        <td>[Перенос из DirectX9 на универсальную платформу Windows (UWP)](porting-your-directx-9-game-to-windows-store.md)</td>
    </tr>
    <tr>
        <td>Перенос из Direct3D 11 в Direct3D 12</td>
        <td>[Перенос из Direct3D 11 в Direct3D 12](https://msdn.microsoft.com/library/windows/desktop/mt431709)</td>
    </tr>
    <tr>
        <td>Перенос из OpenGL ESв Direct3D11</td>
        <td>[Перенос из OpenGL ES2.0 в Direct3D11](port-from-opengl-es-2-0-to-directx-11-1.md)</td>
    </tr>
    <tr>
        <td>Перенос из OpenGL ES в Direct3D11 с помощью ANGLE</td>
        <td>[ANGLE](http://go.microsoft.com/fwlink/p/?linkid=618387)</td>
    </tr>
    <tr>
        <td>Эквиваленты классических API для Windows в UWP</td>
        <td>[Альтернативы Windows API в приложениях универсальной платформы Windows (UWP)](https://msdn.microsoft.com/library/windows/apps/hh464945)</td>
    </tr>
</table>


## <a name="prototype-and-design"></a>Создание прототипа и проектирование


Определив, игру какого типа вы хотите создать и какие средства и графические технологии вы будете использовать в ходе разработки, вы можете приступить к проектированию и созданию прототипа. По своей сути ваша игра является приложением универсальной платформы Windows, поэтому с этого мы и начнем.

### <a name="introduction-to-the-universal-windows-platform-uwp"></a>Введение в универсальную платформу Windows (UWP)

В Windows 10 впервые использована универсальная платформа Windows (UWP), которая предоставляет общую платформу API для разных устройств под управлением Windows 10. UWP является продуктом развития и расширения модели среды выполнения Windows, отточенной до единого унифицированного ядра. Игры для UWP могут вызывать API WinRT, которые являются общими для всех устройств. Поскольку UWP предоставляет гарантированный уровень базового API, вы можете создать один пакет приложения, который будет устанавливаться на различных устройствах под управлением Windows 10. А если вы захотите, то игра сможет вызывать API (в том числе некоторые классические API для Windows из Win32 и .NET), относящиеся к устройствам, на которых будет использоваться игра.

Цель UWP заключается в том, чтобы получить:

-   одну базовую операционную систему;
-   одну платформу приложений;
-   одну социальную игровую сеть;
-   один магазин;
-   Один путь внедрения.

Предлагаем вашему вниманию отличные руководства, в которых подробно обсуждаются приложения универсальной платформы Windows. Эти руководства помогут вам лучше понять платформу.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Знакомство с приложениями универсальной платформы Windows (UWP)</td>
        <td>[Что такое приложение универсальной платформы Windows?](https://msdn.microsoft.com/library/windows/apps/dn726767)</td>
    </tr>
    <tr>
        <td>Обзор UWP</td>
        <td>[Руководство по приложениям UWP](https://msdn.microsoft.com/library/windows/apps/dn894631)</td>
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
        <td>[Начало работы с приложениями для Windows](https://dev.windows.com/getstarted)</td>
    </tr>
    <tr>
        <td>Подготовка к разработке для UWP</td>
        <td>[Подготовка](https://msdn.microsoft.com/library/windows/apps/dn726766)</td>
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
        <td>[Разработка для Windows 10 для абсолютных новичков](https://channel9.msdn.com/Series/Windows-10-development-for-absolute-beginners)</td>
    </tr>
    <tr>
        <td>Объявление о выходе серии видео о разработке для Windows 10 с помощью XAML для абсолютных новичков (запись в блоге)</td>
        <td>[Разработка для Windows 10 для абсолютных новичков](http://blogs.windows.com/buildingapps/2015/09/30/windows-10-development-for-absolute-beginners/)</td>
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
        <td>[Разработка приложений для Windows](https://dev.windows.com/develop)</td>
    </tr>
    <tr>
        <td>Обзор сетевого программирования в UWP</td>
        <td>[Работа в сети и веб-службы](https://msdn.microsoft.com/library/windows/apps/mt280378)</td>
    </tr>
    <tr>
        <td>Применение Windows.Web.HTTP и Windows.Networking.Sockets в играх</td>
        <td>[Поддержка сети в играх](work-with-networking-in-your-directx-game.md)</td>
    </tr>
    <tr>
        <td>Концепции асинхронного программирования в UWP</td>
        <td>[Асинхронное программирование](https://msdn.microsoft.com/library/windows/apps/mt187335)</td>
    </tr>
</table>

### <a name="windows-desktop-apis-to-uwp"></a>API для переноса классических приложений для Windows на UWP

Информация по этим ссылкам упростит перенос классической игры Windows на UWP.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Использование существующего кода C++ для разработки игр UWP</td>
        <td>[Инструкции: использование существующего кода C++ в приложении UWP](https://docs.microsoft.com/cpp/porting/how-to-use-existing-cpp-code-in-a-universal-windows-platform-app)</td>
    </tr>
    <tr>
        <td>API-интерфейсы UWP для API-интерфейсов Win32 и COM</td>
        <td>[API Win32 и COM для приложений UWP](https://docs.microsoft.com/uwp/win32-and-com/win32-and-com-for-uwp-apps)</td>
    </tr>
    <tr>
        <td>Неподдерживаемые функции CRT в UWP</td>
        <td>[Функции CRT, не поддерживаемые в приложениях универсальной платформы Windows](https://msdn.microsoft.com/library/windows/apps/jj606124.aspx)</td>
    </tr>
    <tr>
        <td>Альтернативы API Windows</td>
        <td>[Альтернативы API Windows в приложениях универсальной платформы Windows (UWP)](https://msdn.microsoft.com/library/windows/apps/mt592894.aspx)</td>
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
        <td>[Жизненный цикл приложения](https://msdn.microsoft.com/library/windows/apps/mt243287)</td>
    </tr>
    <tr>
        <td>Использование Microsoft Visual Studio для активации переходов между этапами приложения</td>
        <td>[Активация событий приостановки, возобновления и перевода в фоновый режим для приложений Магазина Windows в Visual Studio](https://msdn.microsoft.com/library/hh974425.aspx)</td>
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
        <td>[Проектирование приложений UWP](https://dev.windows.com/design)</td>
    </tr>
    <tr>
        <td>Проектирование этапов жизненного цикла приложения</td>
        <td>[Руководство по взаимодействию с пользователем — запуск, приостановка и возобновление работы приложения](https://msdn.microsoft.com/library/windows/apps/dn611862)</td>
    </tr>
    <tr>
        <td>Проектирование приложения UWP для Xbox One и телевизоров</td>
        <td>[Проектирование для Xbox и телевизора](https://docs.microsoft.com/windows/uwp/input-and-devices/designing-for-tv)</td>
    </tr>
    <tr>
        <td>Написание приложений для разных форм-факторов устройств (видео)</td>
        <td>[Разработка игр мира Windows Core](http://channel9.msdn.com/Events/GDC/GDC-2015/Designing-Games-for-a-Windows-Core-World)</td>
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
        <td>[Рекомендации: цвет](https://assets.windowsphone.com/499cd2be-64ed-4b05-a4f5-cd0c9ad3f6a3/101_BestPractices_Color_InvariantCulture_Default.zip)</td>
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
        <td>[Рекомендации: оформление текста](http://go.microsoft.com/fwlink/?LinkId=535007)</td>
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
        <td>[Рекомендации: карта пользовательского интерфейса](http://go.microsoft.com/fwlink/?LinkId=535008)</td>
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
        <td>[Руководство по программированию и справочник по API для XAudio2](https://msdn.microsoft.com/library/windows/desktop/hh405049.aspx)</td>
    </tr>
    <tr>
        <td>Создание межплатформенных объектов для обработки звука</td>
        <td>[Обзор протокола XAPO](https://msdn.microsoft.com/library/windows/desktop/ee415735.aspx)</td>
    </tr>
    <tr>
        <td>Введение в понятия обработки звука</td>
        <td>[Звук для игр](working-with-audio-in-your-directx-game.md)</td>
    </tr>
    <tr>
        <td>Обзор Windows Sonic</td>
        <td>[Пространственное звучание](https://msdn.microsoft.com/library/windows/desktop/mt807491.aspx)</td>
    </tr>
    <tr>
        <td>Примеры пространственного звучания Windows Sonic</td>
        <td>[Звуковые примеры, опубликованные группой Xbox Advanced Technology Group](https://github.com/Microsoft/Xbox-ATG-Samples/tree/master/Samples/Audio)</td>
    </tr>
    <tr>
        <td>Узнайте, как интегрировать Windows Sonic в вашей игре (видео)</td>
        <td>[Новые возможности пространственного звука для Xbox и Windows](https://channel9.msdn.com/Events/GDC/GDC-2017/GDC2017-002)</td>
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
        <td>Разработка игр на базе DirectX в UWP</td>
        <td>[Игры и DirectX](index.md)</td>
    </tr>
    <tr>
        <td>Взаимодействие DirectX с моделью приложений UWP</td>
        <td>[Объект приложения и DirectX](about-the-metro-style-user-interface-and-directx.md)</td>
    </tr>
    <tr>
        <td>Видео о разработке графики и DirectX 12 (канал YouTube)</td>
        <td>[Обучение Microsoft DirectX 12 и графике](https://www.youtube.com/channel/UCiaX2B8XiXR70jaN7NK-FpA)</td>
    </tr>
    <tr>
        <td>Обзоры и справочник по DirectX</td>
        <td>[Графика и игры на основе DirectX](https://msdn.microsoft.com/library/windows/desktop/ee663274)</td>
    </tr>
    <tr>
        <td>Справочник и руководство по программированию для Direct3D12</td>
        <td>[Графика Direct3D 12](https://msdn.microsoft.com/library/windows/desktop/dn903821)</td>
    </tr>
    <tr>
        <td>Основы DirectX 12 (видео)</td>
        <td>[Выше мощность — выше производительность: ваша игра на DirectX 12](http://channel9.msdn.com/Events/GDC/GDC-2015/Better-Power-Better-Performance-Your-Game-on-DirectX12)</td>
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
        <td>[Настройка среды для программирования с помощью Direct3D 12](https://msdn.microsoft.com/library/windows/desktop/dn899120.aspx)</td>
    </tr>
    <tr>
        <td>Создание базового компонента</td>
        <td>[Создание базового компонента Direct3D 12](https://msdn.microsoft.com/library/windows/desktop/dn859356.aspx)</td>
    </tr>
    <tr>
        <td>Изменения в Direct3D 12</td>
        <td>[Важные изменения при переходе с Direct3D11 на Direct3D12](https://msdn.microsoft.com/library/windows/desktop/dn899194.aspx)</td>
    </tr>
    <tr>
        <td>Перенос из Direct3D 11 в Direct3D 12</td>
        <td>[Перенос из Direct3D 11 в Direct3D 12](https://msdn.microsoft.com/library/windows/desktop/mt431709.aspx)</td>
    </tr>
    <tr>
        <td>Понятия привязки ресурсов (сопроводительный дескриптор, таблица дескрипторов, куча дескрипторов и корневая подпись) </td>
        <td>[Привязка ресурсов в Direct3D12](https://msdn.microsoft.com/library/windows/desktop/dn899206.aspx)</td>
    </tr>
    <tr>
        <td>Управление памятью</td>
        <td>[Управление памятью в Direct3D12](https://msdn.microsoft.com/library/windows/desktop/dn899198.aspx)</td>
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
        <td>[DirectXTK](http://go.microsoft.com/fwlink/?LinkId=248929)</td>
    </tr>
    <tr>
        <td>Получить набор инструментов DirectX для DirectX 12</td>
        <td>[DirectXTK 12](http://go.microsoft.com/fwlink/?LinkID=615561)</td>
    </tr>
    <tr>
        <td>Получить библиотеку обработки текстур DirectX</td>
        <td>[DirectXTex](http://go.microsoft.com/fwlink/?LinkId=248926)</td>
    </tr>
    <tr>
        <td>Получить библиотеку обработки геометрии DirectXMesh</td>
        <td>[DirectXMesh](http://go.microsoft.com/fwlink/?LinkID=324981)</td>
    </tr>
    <tr>
        <td>Получить библиотеку UVAtlas для создания и упаковывания атласа текстуры isochart.</td>
        <td>[UVAtlas](http://go.microsoft.com/fwlink/?LinkID=512686)</td>
    </tr>
    <tr>
        <td>Получить библиотеку DirectXMath</td>
        <td>[DirectXMath](http://go.microsoft.com/fwlink/?LinkID=615560)</td>
    </tr>
    <tr>
        <td>Поддержка Direct3D 12 в DirectXTK (запись в блоге)</td>
        <td>[Поддержка DirectX 12](https://github.com/Microsoft/DirectXTK/issues/2)</td>
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
        <td>[DirectX 12 на GPU Nvidia](https://developer.nvidia.com/dx12-dos-and-donts-updated)</td>
    </tr>
    <tr>
        <td>Intel: эффективная отрисовка с помощью DirectX 12</td>
        <td>[Отрисовка с помощью DirectX 12 на процессорах Intel Graphics](https://software.intel.com/sites/default/files/managed/4a/38/Efficient-Rendering-with-DirectX-12-on-Intel-Graphics.pdf)</td>
    </tr>
    <tr>
        <td>Intel: поддержка нескольких адаптеров в DirectX 12</td>
        <td>[Реализация явного многоадаптерного приложения с помощью DirectX 12](https://software.intel.com/articles/multi-adapter-support-in-directx-12)</td>
    </tr>
    <tr>
        <td>Intel: учебник по DirectX 12</td>
        <td>[Совместный технический документ Intel, Suzhou Snail и Майкрософт](https://software.intel.com/articles/tutorial-migrating-your-apps-to-directx-12-part-1)</td>
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
        <td>[Плитки, индикаторы событий и уведомления](https://msdn.microsoft.com/library/windows/apps/mt185606)</td>
    </tr>
    <tr>
        <td>Примеры живых плиток и уведомлений</td>
        <td>[Пример уведомлений](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Notifications)</td>
    </tr>
    <tr>
        <td>Шаблоны адаптивных плиток (запись в блоге)</td>
        <td>[Шаблоны адаптивных плиток: схема и документация](http://blogs.msdn.com/b/tiles_and_toasts/archive/2015/06/30/adaptive-tile-templates-schema-and-documentation.aspx)</td>
    </tr>
    <tr>
        <td>Проектирование плиток и индикаторов событий</td>
        <td>[Руководство по плиткам и индикаторам событий](https://msdn.microsoft.com/library/windows/apps/hh465403)</td>
    </tr>
    <tr>
        <td>Приложение Windows 10 для интерактивной разработки шаблонов живых плиток</td>
        <td>[Визуализатор уведомлений](https://www.microsoft.com/store/apps/9nblggh5xsl1)</td>
    </tr>
    <tr>
        <td>Расширение генератора плиток UWP для Visual Studio</td>
        <td>[Средство для создания всех необходимых плиток с использованием одного изображения](https://visualstudiogallery.msdn.microsoft.com/09611e90-f3e8-44b7-9c83-18dba8275bb2)</td>
    </tr>
    <tr>
        <td>Расширение генератора плиток UWP для Visual Studio (запись в блоге)</td>
        <td>[Советы по использованию генератора плиток UWP](https://blogs.windows.com/buildingapps/2016/02/15/uwp-tile-generator-extension-for-visual-studio/)</td>
    </tr>
</table>
 

### <a name="enable-in-app-product-iap-purchases"></a>Поддержка покупок внутренних продуктов приложения (IAP)

IAP (внутренний продукт приложения) — это дополнительный продукт, который игроки могут приобрести во время игры. Внутренними продуктами приложения могут быть новые дополнения, уровни игры, игровые элементы или любой другой продукт, который может понравиться игрокам. При правильном использовании внутренние продукты приложения могут приносить прибыль, при этом улучшая игровой процесс. Распространение информации об IAP игры и их публикация осуществляются через информационную панель Центра разработки для Windows, а возможность покупки из приложения включается в код игры.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Надежные внутренние продукты приложения</td>
        <td>[Поддержка покупок внутренних продуктов приложения](https://msdn.microsoft.com/library/windows/apps/mt219684)</td>
    </tr>
    <tr>
        <td>Потребляемые внутренние продукты приложения</td>
        <td>[Поддержка покупок потребляемых внутренних продуктов приложения](https://msdn.microsoft.com/library/windows/apps/mt219683)</td>
    </tr>
    <tr>
        <td>Сведения о внутреннем продукте приложения и его отправка</td>
        <td>[Отправки IAP](https://msdn.microsoft.com/library/windows/apps/mt148551)</td>
    </tr>
    <tr>
        <td>Отслеживание продаж IAP и демографические сведения о пользователях игры</td>
        <td>[Отчет о приобретениях IAP](https://msdn.microsoft.com/library/windows/apps/mt148538)</td>
    </tr>
</table>
 
### <a name="debugging-performance-optimization-and-monitoring"></a>Отладка, оптимизация и мониторинг производительности

Чтобы оптимизировать производительность, воспользуйтесь преимуществами режима игры в Windows 10, чтобы максимально эффективно использовать текущее оборудование игроков.

Набор средств для оценки производительности Windows (WPT) включает средства мониторинга производительности, создающие подробные профили производительности приложений и операционных систем Windows. Это особенно полезно для контроля за использованием памяти и для повышения производительности игры. Набор средств для оценки производительности Windows включен в Windows 10 SDK и Windows ADK. В этот набор средств входят два независимых средства: Windows Performance Recorder (WPR) и Windows Performance Analyzer (WPA). ProcDump— это программа командной строки, входящая в [Windows Sysinternals](https://technet.microsoft.com/sysinternals/default), которая отслеживает скачки использования ЦП и создает файлы дампа во время сбоев игры. 

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Повышение производительности за счет эксклюзивного или приоритетного доступ к аппаратным ресурсам с помощью API-интерфейсов режима игры</td>
        <td>[Режим игры](https://msdn.microsoft.com/library/windows/desktop/mt808808)</td>
    </tr>
    <tr>
        <td>Получить набор средств для оценки производительности Windows (WPT) из Windows 10 SDK</td>
        <td>[Windows 10 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk)</td>
    </tr>
    <tr>
        <td>Получить набор средств для оценки производительности Windows (WPT) из Windows ADK</td>
        <td>[Windows ADK](https://msdn.microsoft.com/windows/hardware/dn913721.aspx)</td>
    </tr>
    <tr>
        <td>Устранение с помощью Windows Performance Analyzer проблем, при которых пользовательский интерфейс не отвечает (видео)</td>
        <td>[Анализ методом критического пути с помощью WPA](https://channel9.msdn.com/Shows/Defrag-Tools/Defrag-Tools-156-Critical-Path-Analysis-with-Windows-Performance-Analyzer)</td>
    </tr>
    <tr>
        <td>Определение используемых объемов и потерь памяти с помощью Windows Performance Recorder (видео)</td>
        <td>[Используемый объем памяти и утечки памяти](https://channel9.msdn.com/Shows/Defrag-Tools/Defrag-Tools-154-Memory-Footprint-and-Leaks)</td>
    </tr>
    <tr>
        <td>Получить ProcDump</td>
        <td>[ProcDump](https://technet.microsoft.com/sysinternals/dd996900)</td>
    </tr>
    <tr>
        <td>Указания по использованию ProcDump (видео)</td>
        <td>[Настройка ProcDump для создания файлов дампа](https://channel9.msdn.com/Shows/Defrag-Tools/Defrag-Tools-131-Windows-10-SDK)</td>
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
        <td>[Средство оптимизации производительности и отладки для DirectX 12 в Windows](https://blogs.msdn.microsoft.com/pix/2017/01/17/introducing-pix-on-windows-beta/)</td>
    </tr>
    <tr>
        <td>Средства отладки и проверки для разработки на базе D3D12 (видео)</td>
        <td>[Настройка производительности и отладка D3D12 с помощью PIX и проверки графического процессора](https://channel9.msdn.com/Events/GDC/GDC-2017/GDC2017-003)</td>
    </tr>
    <tr>
        <td>Оптимизация графики и производительности (видео)</td>
        <td>[Улучшенная графика и производительность DirectX 12](http://channel9.msdn.com/Events/GDC/GDC-2015/Advanced-DirectX12-Graphics-and-Performance)</td>
    </tr>
    <tr>
        <td>Отладка графики DirectX (видео)</td>
        <td>[Решение сложных проблем графики игры с помощью средств DirectX](http://channel9.msdn.com/Events/GDC/GDC-2015/Solve-the-Tough-Graphics-Problems-with-your-Game-Using-DirectX-Tools)</td>
    </tr>
    <tr>
        <td>Средства Visual Studio 2015 для отладки DirectX 12 (видео)</td>
        <td>[Средства DirectX для Windows 10 в Visual Studio 2015](https://channel9.msdn.com/Series/ConnectOn-Demand/212)</td>
    </tr>
    <tr>
        <td>Руководство по программированию для Direct3D 12</td>
        <td>[Руководство по программированию для Direct3D12](https://msdn.microsoft.com/library/windows/desktop/dn903821)</td>
    </tr>
    <tr>
        <td>Сочетание DirectX и XAML</td>
        <td>[Межпрограммное взаимодействие DirectX и XAML](directx-and-xaml-interop.md)</td>
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
        <td>[Руководство по разработке для глобальной аудитории](https://msdn.microsoft.com/library/windows/apps/xaml/mt186453.aspx)</td>
    </tr>
    <tr>
        <td>Объединение языков, культур и технологий</td>
        <td>[Интернет-ресурс с языковыми соглашениями и стандартной терминологией Майкрософт](http://www.microsoft.com/Language/Default.aspx)</td>
    </tr>
</table>

## <a name="submitting-and-publishing-your-game"></a>Отправка и публикация игры

Следующие руководства и сведения призваны максимально облегчить процесс публикации и отправки.

### <a name="publishing"></a>Публикация

Для публикации и управления пакетами игры вы будете использовать новую унифицированную информационную панель Центра разработки для Windows.

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Публикация приложения Центра разработки для Windows</td>
        <td>[Публикация приложений для Windows](https://dev.windows.com/publish)</td>
    </tr>
    <tr>
        <td>Расширенная публикация с помощью Центра разработки для Windows (GDN)</td>
        <td>[Руководство по расширенной публикации с помощью информационной панели Центра разработки для Windows](https://developer.xboxlive.com/en-us/windows/documentation/Pages/home.aspx)</td>
    </tr>
    <tr>
        <td>Использование Azure Active Directory (AAD) для добавления пользователей в учетную запись Центра разработки</td>
        <td>[Управление пользователями учетных записей](https://docs.microsoft.com/windows/uwp/publish/manage-account-users)</td>
    </tr>   
    <tr>
        <td>Оценка игры (запись блога)</td>
        <td>[Единый процесс для присвоения возрастных категорий с помощью системы IARC](https://blogs.windows.com/buildingapps/2016/01/06/now-available-single-age-rating-system-to-simplify-app-submissions/)</td>
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
        <td>[Распространение приложений UWP следующего поколения: создание расширяемых приложений с поддержкой потоков на основе компонентов](https://channel9.msdn.com/Events/Build/2017/B8093)</td>
    </tr>
    <tr>
        <td>Разделение и группировка содержимого для потоковой установки</td>
        <td>[Потоковая установка приложений UWP](../packaging/streaming-install.md)</td>
    </tr>
    <tr>
        <td>Создание дополнительных пакетов, например DLC для игр</td>
        <td>[Разработка дополнительных пакетов и связанных наборов](../packaging/optional-packages.md)</td>
    </tr>
    <tr>
        <td>Создание пакета игры UWP</td>
        <td>[Создание пакетов приложений](../packaging/index.md)</td>
    </tr>
    <tr>
        <td>Создание пакета игры на базе DirectX для UWP</td>
        <td>[Создание пакета игры на базе DirectX для UWP](package-your-windows-store-directx-game.md)</td>
    </tr>
    <tr>
        <td>Создание пакета игры в качестве стороннего разработчика (запись блога)</td>
        <td>[Создание пакетов без доступа к учетной записи магазина издателя](https://blogs.windows.com/buildingapps/2015/12/15/building-an-app-for-a-3rd-party-how-to-package-their-store-app/)</td>
    </tr>
    <tr>
        <td>Создание пакетов приложений и их наборов с помощью MakeAppx</td>
        <td>[Создание пакетов с помощью упаковщика MakeAppx.exe](https://docs.microsoft.com/windows/uwp/packaging/create-app-package-with-makeappx-tool)</td>
    </tr>
    <tr>
        <td>Цифровая подпись файлов с помощью SignTool</td>
        <td>[Подпись файлов и проверка подписи с помощью SignTool](https://msdn.microsoft.com/library/windows/desktop/aa387764)</td>
    </tr>    
    <tr>
        <td>Отправка игры и управление версиями</td>
        <td>[Отправка пакетов приложений](https://msdn.microsoft.com/library/windows/apps/mt148542)</td>
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
        <td>Соглашение с разработчиком приложений Магазина Windows</td>
        <td>[Соглашение с разработчиком приложений](https://msdn.microsoft.com/library/windows/apps/hh694058)</td>
    </tr>
    <tr>
        <td>Правила публикации приложений в Магазине Windows</td>
        <td>[Политики Магазина Windows](https://msdn.microsoft.com/library/windows/apps/dn764944)</td>
    </tr>
    <tr>
        <td>Способы предотвращения некоторых распространенных проблем сертификации приложений</td>
        <td>[Недопущение распространенных ошибок при сертификации](https://msdn.microsoft.com/library/windows/apps/jj657968)</td>
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
        <td>[Схема манифеста Магазина (Windows 10)](https://msdn.microsoft.com/library/windows/apps/mt617335)</td>
    </tr>
</table>
 

## <a name="game-lifecycle-management"></a>Управление жизненным циклом игры


Завершив создание игры и отправив ее, не считайте, что вы все сделали. Возможно, вы завершили разработку первой версии, однако путешествие вашей игры на рынке только начинается. Вам будет необходимо следить за пользованием и получать отчеты об ошибках, реагировать на отзывы пользователей и публиковать обновления игры.

### <a name="windows-dev-center-analytics-and-promotion"></a>Аналитика и рекламирование в Центре разработки для Windows

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Приложение Центра разработки</td>
        <td>[Приложение Центра разработки для Windows 10 для просмотра показателей опубликованных приложений.](https://www.microsoft.com/store/apps/dev-center/9nblggh4r5ws)</td>
    </tr>  
    <tr>
        <td>Аналитика Центра разработки для Windows</td>
        <td>[Аналитика](https://msdn.microsoft.com/library/windows/apps/mt148522)</td>
    </tr>
    <tr>
        <td>Ответ на отзывы клиентов</td>
        <td>[Реакция на отзывы пользователей](https://msdn.microsoft.com/library/windows/apps/mt148546)</td>
    </tr>
    <tr>
        <td>Способы продвижения игры</td>
        <td>[Продвижение приложений](https://dev.windows.com/store-promotion)</td>
    </tr>
</table>
 

### <a name="visual-studio-application-insights"></a>Visual Studio Application Insights

Статистика Visual Studio Application Insights позволяет получать аналитические сведения о производительности, телеметрии и использовании опубликованной игры. Статистика Application Insights помогает обнаруживать и устранять проблемы после выхода игры, постоянно контролировать и совершенствовать игровой процесс, понимать характер взаимодействия игроков с игрой. Статистика Application Insights работает путем добавления SDK в приложение, которое отправляет телеметрию на [портал Azure](http://portal.azure.com/).

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Аналитика производительности и использования приложения</td>
        <td>[Visual Studio Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-get-started/)</td>
    </tr>
    <tr>
        <td>Включение статистики Application Insights в приложениях для Windows</td>
        <td>[Статистика Application Insights для приложений для Windows Phone и Магазина](https://azure.microsoft.com/documentation/articles/app-insights-windows-get-started/)</td>
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
        <td>[GameAnalytics](http://www.gameanalytics.com/)</td>
    </tr>
    <tr>
        <td>Подключение игры UWP к Google Analytics</td>
        <td>[Получить Windows SDK для Google Analytics](https://github.com/dotnet/windows-sdk-for-google-analytics)</td>
    </tr>
    <tr>
        <td>Узнайте, как использовать Windows SDK для Google Analytics (видео)</td>
        <td>[Начало работы с Windows SDK для Google Analytics](https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Creators-Update/Getting-started-with-the-Windows-SDK-for-Google-Analytics)</td>
    </tr>    
    <tr>
        <td>Использование рекламных объявлений об установке в приложении Facebook для продвижения игры среди пользователей Facebook</td>
        <td>[Получить Windows SDK для Facebook](https://github.com/Microsoft/winsdkfb)</td>
    </tr>
    <tr>
        <td>Узнайте, как использовать рекламные объявления об установке в приложении Facebook (видео)</td>
        <td>[Начало работы с Windows SDK для Facebook](https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Creators-Update/Getting-started-with-Facebook-App-Install-Ads)</td>
    </tr>
    <tr>
        <td>Использование Vungle для добавления видеорекламы в игры</td>
        <td>[Получить Windows SDK для Vungle](https://v.vungle.com/sdk)</td>
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
        <td>[Нумерация версий пакета](https://msdn.microsoft.com/library/windows/apps/mt188602)</td>
    </tr>
    <tr>
        <td>Руководство по управлению пакетами игры</td>
        <td>[Руководство по управлению пакетами приложения](https://msdn.microsoft.com/library/windows/apps/mt188602)</td>
    </tr>
</table>


## <a name="adding-xbox-live-to-your-game"></a>Добавление Xbox Live в игру

> **Примечание.** Если вы хотите создавать игры с поддержкой Xbox Live, вам доступно несколько вариантов. Сведения о различных приложений см. в разделе [Обзор программ для разработчиков](../xbox-live/developer-program-overview.md).

<table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr>
        <td>Обзор Xbox Live</td>
        <td>[Руководство по Xbox Live для разработчиков](../xbox-live/index.md)</td>
    </tr>
    <tr>
        <td>Описание функций, доступных в разных программах</td>
        <td>[Обзор программ для разработчиков: таблица функций](../xbox-live/developer-program-overview.md#feature-table)</td>
    </tr>
    <tr>
        <td>Получение данных от служб Xbox Live</td>
        <td>[Введение в API-интерфейсы Xbox Live](../xbox-live/introduction-to-xbox-live-apis.md)</td>
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
        <td>[Начало работы с программой Xbox Live Creators Program](../xbox-live/get-started-with-creators/get-started-with-xbox-live-creators.md)</td>
    </tr>
    <tr>
        <td>Добавление Xbox Live в игру</td>
        <td>[Пошаговое руководство по интеграции программы Xbox Live Creators Program](../xbox-live/get-started-with-creators/creators-step-by-step-guide.md)</td>
    </tr>
    <tr>
        <td>Добавление Xbox Live в игру UWP, созданной с помощью Unity</td>
        <td>[Начало разработки игры в рамках программы Xbox Live Creators Program с помощью игровой системы Unity](../xbox-live/get-started-with-creators/develop-creators-title-with-unity.md)</td>
    </tr>
    <tr>
        <td>Узнайте, как интегрировать межплатформенные возможности Xbox Live в играх UWP (видео)</td>
        <td>[Программа Xbox Live Creators Program](https://channel9.msdn.com/Events/GDC/GDC-2017/GDC2017-005)</td>
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
        <td>[Начало работы с Xbox Live в качестве управляемого партнера или разработчика ID](../xbox-live/get-started-with-partner/get-started-with-xbox-live-partner.md)</td>
    </tr>
    <tr>
        <td>Добавление Xbox Live в игру</td>
        <td>[Пошаговое руководство по интеграции Xbox Live для управляемых партнеров и членов ID](../xbox-live/get-started-with-partner/partners-step-by-step-guide.md)</td>
    </tr>
    <tr>
        <td>Добавление Xbox Live в игру UWP, созданной с помощью Unity</td>
        <td>[Добавление поддержки Xbox Live в Unity для UWP с использованием сервера сценариев IL2CPP для членов ID и управляемых партнеров](../xbox-live/get-started-with-partner/partner-unity-uwp-il2cpp.md)</td>
    </tr>
    <tr>
        <td>Требования к играм, использующим Xbox Live (GDN)</td>
        <td>[Требования к Xbox для Xbox Live в Windows 10](http://go.microsoft.com/fwlink/?LinkId=533217)</td>
    </tr>
    <tr>
        <td>Обзор разработки игр Xbox Live (видео)</td>
        <td>[Разработка с использованием Xbox Live для Windows 10](http://channel9.msdn.com/Events/GDC/GDC-2015/Developing-with-Xbox-Live-for-Windows-10)</td>
    </tr>
    <tr>
        <td>Кроссплатформенное проведение матчей (видео)</td>
        <td>[Многопользовательский режим Xbox Live: знакомство со службой кросс-платформенного проведения матчей и игр](http://channel9.msdn.com/Events/GDC/GDC-2015/Xbox-Live-Multiplayer-Introducing-services-for-cross-platform-matchmaking-and-gameplay)</td>
    </tr>
    <tr>
        <td>Многопользовательские игры на разных устройствах в Fable Legends (видео)</td>
        <td>[Fable Legends: многопользовательские игры на разных устройствах с Xbox Live](http://channel9.msdn.com/Events/GDC/GDC-2015/Fable-Legends-Cross-device-Gameplay-with-Xbox-Live)</td>
    </tr>
    <tr>
        <td>Статистика и достижения Xbox Live (видео)</td>
        <td>[Рекомендации по использованию облачной статистики и достижений пользователя в Xbox Live](http://channel9.msdn.com/Events/GDC/GDC-2015/Best-Practices-for-Leveraging-Cloud-Based-User-Stats-and-Achievements-in-Xbox-Live)</td>
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
        <td>[Видео с больших конференций, таких как GDC и //build](https://docs.microsoft.com/windows/uwp/gaming/game-development-videos)</td>
    </tr>
    <tr>
        <td>Независимая разработка игр (видео)</td>
        <td>[Новые возможности для независимых разработчиков](http://channel9.msdn.com/Events/GDC/GDC-2015/New-Opportunities-for-Independent-Developers)</td>
    </tr>
    <tr>
        <td>Особенности многоядерных мобильных устройств (видео)</td>
        <td>[Стабильная производительность игры на многоядерных мобильных устройствах](http://channel9.msdn.com/Events/GDC/GDC-2015/Sustained-gaming-performance-in-multi-core-mobile-devices)</td>
    </tr>
    <tr>
        <td>Разработка классических игр для Windows 10 (видео)</td>
        <td>[Компьютерные игры для Windows 10](http://channel9.msdn.com/Events/GDC/GDC-2015/PC-Games-for-Windows-10)</td>
    </tr>
</table>



 

 

 
