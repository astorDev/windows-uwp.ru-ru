---
author: joannaleecy
title: "Использование облачных служб при разработке игр UWP"
description: "Подробнее о внедрении облака в качестве серверного компонента при создании игр UWP."
ms.assetid: 1a7088e0-0d7b-11e6-8e05-0002a5d5c51b
ms.sourcegitcommit: b25f02dc4ebcf960882e64f66f0306a8e584ebbd
ms.openlocfilehash: d9b252783213f0c6a82944729f98c84e21d56535

---
#  Использование облачных служб при разработке игр UWP

Универсальная платформа Windows (UWP) в Windows 10 предлагает набор API-интерфейсов, которые можно использовать для разработки игр для разных устройств Майкрософт. При разработке игр для разных платформ и устройств можно использовать облачную серверную среду для масштабирования игры с учетом текущих потребностей в ресурсах.

##  Что такое облачные вычисления?

В рамках технологии облачных вычислений ИТ-ресурсы и приложения предоставляются по запросу через Интернет, а затем используются для хранения и обработки данных на ваших устройствах. Понятие _облако_ является образным выражением, использующимся для обозначения доступности большого объема ресурсов, расположенных вне локальной среды или устройства, к которым можно осуществлять доступ из разных местоположений.
Технология облачных вычислений предлагает новый способ потребления ресурсов и программного обеспечения. Теперь пользователям не нужно приобретать весь продукт или ресурсы сразу — они могут пользоваться платформой, программным обеспечением и ресурсами как услугой. Поставщики облачных служб часто начисляют плату за объем использованных ресурсов или в соответствии с тарифными планами.

##  Преимущества облачных служб

Одним из преимуществ использования облачных служб для разработки игр является отсутствие необходимости в приобретении физических аппаратных серверов, необходимо лишь производить оплату за объем потребленных ресурсов или в соответствии с тарифным планом по мере их потребления. Это позволяет снизить риски, связанные с разработкой новой игры. 

Другим преимуществом является то, что игра может обращаться к большому объему облачных ресурсов с целью масштабирования (эффективное управление любым внезапным и резким увеличением количества одновременно подключенных игроков; обработка сложных внутриигровых процессов в режиме реального времени или удовлетворение жестких требований, предъявляемых к данным). Это обеспечивает стабильную производительность игры в круглосуточном режиме. Кроме того, к облачным ресурсам можно осуществлять доступ с любого устройства, работающего на базе любой платформы, в любой точке мира. Это значит, что вы можете рассказать о своей игре любому заинтересованному пользователю.

Предоставление захватывающего игрового процесса для игроков играет важную роль. Поскольку игровые серверы, работающие в облаке, не зависят от клиентских обновлений, такие серверы могут обеспечить более управляемую и защищенную среду для игры в целом.   Облако также позволяет обеспечить однородность игрового процесса за счет отказа от привязки к текущей работоспособности клиента, используя вместо этого серверную игровую логику. Кроме того, для предоставления более полного взаимодействия с игрой можно выполнять настройку подключений между службами: привязывать внутриигровые покупки к разным способам оплаты, объединять различные игровые сети и отправлять внутриигровые обновления в популярные социальные сети, такие как Facebook и Twitter. 

Также вы можете использовать выделенные облачные серверы для создания стабильного игрового мира и игровых сообществ, сбора и анализа данных об игре для улучшения игрового процесса и оптимизации работы вашей модели монетизации игры.

Помимо этого, облачные службы можно использовать для создания игр, требующих применения расширенных возможностей управления игровыми данными, например игр для социальных сетей с асинхронной механикой многопользовательского режима.

##  Как компании-разработчики игр используют технологию облачных вычислений?

Узнайте о том, как другие разработчики реализовали облачные решения в своих играх.

<table>
    <colgroup>
    <col width="10%" />
    <col width="30%" />
    <col width="30%" />
    <col width="30%" />
    </colgroup>
    <tr class="header">
        <th>Разработчик</th>
        <th>Описание</th>
        <th>Основные игровые сценарии</th>
        <th>Подробнее</th>
    </tr>
    <tr>
        <td>[343 Industries](https://www.halowaypoint.com/)</td>
        <td>В игре_Halo 5: Guardians_ в качестве места для социального взаимодействия игроков используется платформа [Halo: Spartan Companies](https://www.halowaypoint.com/spartan-companies), работающая на базе службы Microsoft Azure DocumentDB, которая была выбрана для этой цели за счет высокой скорости работы и гибкости благодаря встроенной возможности автоматического индексирования.</td>
        <td>
            <ul>
                <li>Масштабируемый уровень данных для работы с процессами создания групп игроков и управления ими в многопользовательском режиме <li>Интеграция игрового пространства и социальных сетей <li>Отправка запросов в режиме реального времени посредством использования различных атрибутов <li>Синхронизация игровых достижений и статистики </ul>
        </td>
        <td>
            <ul>
                <li>[Реализация социального игрового процесса с помощью Azure DocumentDB](https://azure.microsoft.com/en-us/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/)</td>
            </ul>
    </tr>
    <tr>
        <td>[Illyriad Games](http://web.ageofascent.com/)</td>
        <td>Компания Illyriad Games разработала игру _Age of Ascent_, относящуюся к жанру массовых многопользовательских сетевых игр, действия которой разворачиваются в трехмерной космическом пространстве и играть в которую можно на устройствах с современными браузерами. В эту игру можно играть на ПК, ноутбуках, мобильных телефонах и других мобильных устройствах без подключаемых модулей. В игре используются технологии ASP.NET Core, HTML5, WebGL и Microsoft Azure.</td>
        <td>
            <ul>
                <li>Кроссплатформенная браузерная игра <li>Единый, большой, стабильный открытый мир <li>Обработка сложных внутриигровых процессов в режиме реального времени <li>Масштабирование по мере изменения количества игроков </ul>
        </td>
        <td>
            <ul>
                <li>[Управление компонентами игры как микрослужбами с помощью Azure Service Fabric (видеоролик)](https://channel9.msdn.com/Events/Build/2016/KEY02#time=57m20s)  
                <li>[Интервью с разработчиками Age of Ascent (видеоролик)](https://channel9.msdn.com/Shows/Azure-Friday/Age-of-Ascent-from-Illyriad-Powered-by-Azure-Service-Fabric-and-ASPNET)
            </ul>
        </td>
    </tr>
    <tr>
        <td>[Next Games](http://www.nextgames.com/)</td>
        <td>Компания Next Games является разработчиком видеоигры _The Walking Dead: No Man's Land_, созданной по мотивам сериала, транслируемого телеканалом AMC. В качестве серверной части в игре The Walking Dead используется платформа Azure. В премьерные выходные количество скачиваний игры составило 1 000 000 раз и в течение первой недели после выпуска она получила статус "Лучшая бесплатная игра для iPhone и iPad" в американском магазине App Store, "Лучшее бесплатное приложение" в 12 странах и "Лучшая бесплатная игра" в 13 странах.
        </td>
        <td>
            <ul>
                <li>Поддержка разных платформ <li>Пошаговый многопользовательский режим <li>Гибкое масштабирование производительности </ul>
        </td>
        <td>
            <ul>
                <li>[Интервью с Калле Хийтолла (Kalle Hiitola), техническим директором Next Games (видеоролик)](https://channel9.msdn.com/Blogs/AzureDocumentDB/azure-documentdb-walking-dead)
                <li>[Использование DocumentDB в процессе создания игры Walking Dead для ускорения процесса разработки и реализации более захватывающего игрового процесса](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/)
            </ul>
    </tr>
    </td>
        <td>[Pixel Squad](http://www.crimecoast.com/)</td>
        <td>Компания Pixel Squad разработала игру _Crime Coast_ на базе игрового движка Unity с применением платформы Azure. _Crime Coast_ — это социальная игра в жанре стратегии, доступная на платформах Android, iOS и Windows. В игре использовались следующие технологии: хранилище больших двоичных объектов Azure, управляемый кэш Redis для Azure, виртуальные машины IIS с балансировкой нагрузки и центр уведомлений Майкрософт. Узнайте, как компания управляла масштабированием игры и наплывами игроков при одновременном подключении 5000 игроков.
        </td>
        <td>
            <ul>
                <li>Поддержка разных платформ <li>Многопользовательская сетевая игра <li>Масштабирование по мере изменения количества игроков </ul>
        </td>
        <td>
            <ul>
                <li>[Использование облачных служб Microsoft Azure в массовой многопользовательской сетевой игре Crime Coast](https://channel9.msdn.com/Blogs/The-Game-Blog/BizSpark-Interview-with-Pixel-Squad-How-the-used-Azure-Cloud-Services-to-make-an-MMO-with-a-3-man-te)
            </ul>
        </td>
    </tr> 
</table>

    
### Прочие ссылки

* [Представители компаний Hitcents, Game Troopers и InnoSpark рассказывают об уникальных возможностях Azure](http://news.microsoft.com/features/game-developers-use-microsoft-azure-as-secret-sauce-for-scale-and-growth-2/)
* [Начинающие компании используют Azure в рамках программы BizSpark](https://blogs.technet.microsoft.com/bizspark_featured_startups/2015/09/25/azure-open-for-gaming-startups/)


## Проектирование облачной серверной среды

В то время как разработчики и геймдизайнеры обсуждают, какие компоненты и возможности стоит добавить в игру, также полезно задуматься о там, какой должна быть серверная инфраструктура игры. Облако Azure можно использовать в качестве серверной части для игры, когда речь идет о разработке игр для различных устройств, работающих на базе разных основных платформ.

### Пошаговые обучающие руководства

* [Практические занятия с конференции Build 2016: использование службы приложений Microsoft Azure и внутреннего сервера Microsoft SQL Azure для хранения игровых очков](https://github.com/Microsoft-Build-2016/CodeLabs-GameDev-6-Azure)
* [Разработка стратегии повышения вовлеченности пользователей в игры для мобильных устройств](https://azure.microsoft.com/en-us/documentation/articles/mobile-engagement-gaming-scenario/)
* [Использование платформы Azure Mobile Engagement для развертывания Unity в среде iOS](https://azure.microsoft.com/en-us/documentation/articles/mobile-engagement-unity-ios-get-started/)

### Общие сведения о понятиях "инфраструктура как услуга", "платформа как услуга" и "ПО как услуга"

Сначала необходимо решить, какой уровень обслуживания лучше всего подойдет для вашей игры. Зная различия следующих трех служб, вам будет проще выбрать подход для создания серверной части.

* [Инфраструктура как услуга (IaaS)](https://azure.microsoft.com/en-us/overview/what-is-iaas/)

    Инфраструктура как услуга (IaaS) — это вычислительная инфраструктура, мгновенно выделяемая и управляемая через Интернет. Она предоставляет в ваше распоряжение большое количество компьютеров, доступных для оперативного горизонтального и вертикального масштабирования производительности игры по требованию. Модель "инфраструктура как услуга" помогает избежать расходов и трудностей, связанных с приобретением собственных физических серверов и других ИТ-ресурсов ЦОД, а также управлением ими.

* [Платформа как услуга (PaaS)](https://azure.microsoft.com/en-us/overview/what-is-paas/)

    Как и в случае с IaaS, в состав модели "платформа как услуга" входят средства управления ресурсами ИТ-инфраструктуры, такими как серверы, хранилище и сетевое оборудование. Помимо того, что при использовании модели "платформа как услуга" отпадает необходимость в приобретении физических серверов и инфраструктуры ЦОД, она также позволяет избежать расходов и трудностей, связанных с покупкой лицензий на программное обеспечение и управлением ими, базовой инфраструктуры приложений, ПО промежуточного уровня, средств разработки и других ресурсов.

* Программное обеспечение как услуга (SaaS)

    Модель "ПО как услуга", как правило, позволяет подключаться к готовым приложениям, размещенным в среде уже имеющейся облачной платформы. Она разработана для того, чтобы еще больше упростить процесс развертывания и запуска вашей игры с помощью сторонней службы.


### Проектирование инфраструктуры игры с помощью платформы Azure

Далее приведены некоторые способы использования возможностей облака Azure при создании игр. Azure поддерживает работу с платформами Windows и Linux, а также всем знакомыми технологиями с открытым исходным кодом, такими как Ruby, Python, Java и PHP. Дополнительную информацию см. на веб-сайте, посвященном [облаку Azure](https://azure.microsoft.com).

| Цель использования                 | Сценарии использования                            | Продукт                      | Возможности продукта                               |
|-----------------------------------|-----------------------------------------------|---------------------------------------|----------------------------------------------------|
| Размещение домена в облаке     | Быстрый отклик на запросы к DNS            | [Azure DNS](https://azure.microsoft.com/services/dns/) | Размещение домена в облаке Azure для достижения высокой производительности и доступности  |
| Служба единого входа, управление проверкой личности      | Управление доступом игроков и проверка их личности  | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) | Единый вход во все облачные и локальные веб-приложения с помощью многофакторной проверки подлинности            |
| Создание игры в рамках модели "инфраструктура как услуга" (IaaS)      | Размещение игры на виртуальных машинах в облаке       | [Виртуальные машины Azure](https://azure.microsoft.com/services/virtual-machines/) | Возможность масштабирования от 1 экземпляра виртуальной машины до тысяч экземпляров в качестве игровых серверов, а также встроенные средства виртуализации сети и балансировки нагрузки; обеспечение однородности гибридной облачной среды и локальных систем           |
| Использование модели "платформа как услуга" (PaaS) для создания веб-приложений или мобильных игр            | Размещение игры на управляемой платформе                | [Служба приложений Azure](https://azure.microsoft.com/services/app-service/) | Модель "платформа как услуга" для создания веб-сайтов или мобильных игр (виртуальные машины Azure с ПО промежуточного слоя, средствами разработки, базовой инфраструктурой приложений и средствами управления базами данных)   |
| Облачное хранилище для игровых данных       | Хранение актуальных игровых данных в облаке и их отправка на клиентские устройства | [Хранилище BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)| Отсутствие ограничений на типы хранимых файлов; хранилище объектов для больших объемов неструктурированных данных, таких как изображения, аудиозаписи, видеоматериалы и др.  |
| Хранилище таблиц для временного хранения данных| Таблицы для временного хранения игровых транзакций (изменений игровых состояний) | [Хранилище таблиц Azure](https://azure.microsoft.com/services/storage/tables/)| Возможность хранения игровых данных в формате гибкой схемы в соответствии с требованиями игры |
| Добавление в очередь игровых транзакций/запросов| Обработка игровых транзакций в виде очереди | [Хранилище очередей Azure](https://azure.microsoft.com/services/storage/queues/)| Хранилища очередей обрабатывают неожиданные пики трафика и предотвращают перегрузку серверов в случае внезапного увеличения потока запросов в игре.   |
| Масштабируемая реляционная база данных для игры| База данных для структурированного хранения реляционных данных, таких как игровые транзакции | [База данных Azure SQL](https://azure.microsoft.com/services/sql-database/)| "База данных SQL как услуга" ([Сравнение с SQL Server на виртуальных машинах Azure](https://azure.microsoft.com/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas/))  |
| Масштабируемая распределенная база данных для игры с низкой задержкой при доступе к данным| Быстрая обработка запросов на чтение и запись игровых и пользовательских данных на основе гибкой схемы | [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)| "База данных документов NoSQL как услуга" с низкой задержкой при доступе к данным   |
| Использование собственного ЦОД со службами Azure | Извлечение игры из вашего собственного ЦОД и ее отправка на клиентские устройства | [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) | Дает возможность вашей организации предоставлять службы Azure из собственного центра обработки данных и добиваться более высоких результатов  |
| Передача больших порций данных| Возможность отправки пользователям больших файлов, таких как игровые изображения, звуковые записи и видеоматериалы из ближайшей сети доставки содержимого (CDN) с помощью Azure CDN  | [Сеть кэширующих серверов Azure CDN](https://azure.microsoft.com/services/cdn/) | В основе Azure CDN используется современная топология сети, состоящая из больших, централизованно расположенных серверных узлов, что позволяет выполнять обработку внезапных скачков трафика и высоких нагрузок с целью значительного улучшения показателей скорости и доступности и, как следствие, кардинального повышения удобства работы пользователей  |
| Низкая задержка при доступе к данным               | Кэширование данных для создания высокопроизводительных и масштабируемых игр при повышении степени управляемости и обеспечении гарантированной изоляции данных; также можно использовать для улучшения функции создания игровых матчей. | [Кэш Redis для Azure](https://azure.microsoft.com/services/cache/) | Доступ к данным с высокой пропускной способностью и стабильно небольшой задержкой для создания быстрых и масштабируемых приложений Azure  |
| Высокая масштабируемость, низкая задержка | Обработка периодических изменений количества игроков, а также малое время задержки операций чтения и записи | [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Возможность реализации наиболее сложных сценариев, требующих обработки большого количества данных с низкой задержкой, а также надежное масштабирование для одновременной обработки большего количества пользователей. Решение Service Fabric позволяет разрабатывать игры без необходимости в создании отдельного хранилища или кэша, требующихся для работы приложений без сохранения состояния |
| Возможность сбора миллионов событий в секунду с миллионов устройств                         | Обработка миллиона потоков событий в секунду, поступающих с подключенных устройств | [Концентраторы событий Azure](https://azure.microsoft.com/services/event-hubs/) | Сбор телеметрии из игр, веб-сайтов, приложений и устройств в масштабе облака  |
| Обработка игровых данных в режиме реального времени  | Анализ игровых данных в режиме реального времени для улучшения игрового процесса| [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) | Потоковая обработка данных в режиме реального времени в облаке  |
| Разработка игрового процесса, предвосхищающего действия пользователей         | Создание персонализированного и динамичного игрового процесса на основе данных о действиях игроков  | [Машинное обучение Azure](https://azure.microsoft.com/services/machine-learning/) | Полностью управляемая облачная служба, которая позволяет легко создавать и развертывать решения для прогнозной аналитики, а также предоставлять к ним общий доступ  |
| Сбор и анализ игровых данных| Массовая параллельная обработка данных как из реляционных, так и из нереляционных баз данных | [Хранилище данных Azure SQL](https://azure.microsoft.com/services/sql-data-warehouse/)| "Эластичное хранилище данных как услуга" с возможностями корпоративного уровня   |
| Создание маркетинговых кампаний для повышения эффективности процессов вовлечения и удержания пользователей  | Отправка push-уведомлений целевым группам игроков для повышения вовлеченности в игру и мотивирования пользователей для совершения определенных внутриигровых действий на основе собранных аналитических данных | [Мобильный охват](https://azure.microsoft.com/services/mobile-engagement/) |  Увеличение времени пребывания игроков в игре и повышение лояльности пользователей на всех основных платформах — iOS, Android, Windows и Windows Phone |


##  Ресурсы для начинающих компаний и разработчиков

* [Программа Microsoft BizSpark](https://www.microsoft.com/bizspark/)

    Microsoft BizSpark — это международная программа, помогающая начинающим компаниям достичь успеха, в рамках которой компании-участники получают бесплатный доступ к облачным службам, программному обеспечению Azure и технической поддержке Майкрософт. Компании-участники программы BizSpark получают Visual Studio Enterprise с подпиской MSDN для пяти разработчиков, а также ресурсы облачного хостинга Microsoft Azure на сумму 150 долл. США для каждого сотрудника ежемесячно. В сумме пять разработчиков компании-участника программы получают кредит в размере 750 долл. США/мес, который они могут использовать для оплаты служб Azure. Стать участником программы BizSpark могут частные начинающие компании, с момента образования которых прошло не больше 5 лет и годовой доход которых составляет менее 1 млн долл. США. В корпорации Майкрософт придерживаются мнения, что помощь начинающим компаниям в развитии их бизнеса положительно сказывается на построении плодотворных и долгосрочных партнерских отношений.
    
* [Программа ID@Xbox](http://www.xbox.com/Developers/id)

    Если вы хотите добавить в свою игру для Windows 10 возможности Xbox Live, такие как многопользовательский режим, создание игровых матчей на разных платформах, счет игрока, достижения и списки лидеров, зарегистрируйтесь в программе ID@Xbox, чтобы получить инструменты и поддержку, необходимые для успешного воплощения своих творческих идей. Перед подачей заявки на участие в программе ID@Xbox зарегистрируйте учетную запись разработчика в [Центре разработки для Windows](https://developer.microsoft.com/windows/programs/join).

## "Программное обеспечение как услуга" в качестве серверной части игры

Следующие компании предоставляют облачную серверную среду для создания игр, используя возможности основных поставщиков облачных служб, позволяя вам сосредоточиться непосредственно на самом процессе разработки игры.

* [GameSparks](http://www.gamesparks.com/)

    GameSparks — облачная платформа разработки ПО для разработчиков игр, предоставляющая возможность создавать всю серверную часть игры.

* [Photon Engine](https://www.photonengine.com/en/Photon)

    Photon — это независимый сетевой движок и платформа для реализации многопользовательского режима в играх. Компонент этого движка под названием Photon Cloud позволяет воспользоваться преимуществами модели "ПО как услуга" и, таким образом, представляет собой полностью управляемую службу. Вы можете полностью сосредоточиться на разработке клиента приложения, тогда как выполнение задач по размещению, предоставлению серверной части и масштабированию игры возьмет на себя компания Exit Games.

* [PlayFab](https://playfab.com/)

    Компания PlayFab помогает быстро и просто реализовать серверную часть игры и предоставляет высококлассные средства управления онлайн-играми для мобильных устройств, ПК или игровых приставок.

## Дополнительные ссылки

* [Руководство по разработке игр для Windows 10](https://msdn.microsoft.com/windows/uwp/gaming/e2e)
* [Платформа Microsoft Azure](https://azure.microsoft.com/)
* [Программа Microsoft BizSpark](https://www.microsoft.com/bizspark/)
* [Программа ID@Xbox](http://www.xbox.com/Developers/id)


 

 



<!--HONumber=Jun16_HO4-->

