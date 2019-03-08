---
title: Обзор программы для разработчиков
description: Дополнительные сведения о различных разработчиков программ, можно использовать, Xbox Live.
ms.assetid: 1166308a-4079-41b4-8550-ce04b82b4f72
ms.date: 05/30/2018
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, программы для разработчиков, создатели
ms.localizationpriority: medium
ms.openlocfilehash: 05abd3f28328f4418f5a8a772049b3869b488ffc
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57603889"
---
# <a name="developer-program-overview"></a>Обзор программы для разработчиков

Если вы хотите разрабатывать Xbox Live включить заголовки, доступны несколько вариантов для вас. Каждый предлагает различные уровни временных затрат с вашей стороны, функции, доступные для вас и варианты поддержки.

## <a name="xbox-live-creators-program"></a>Программа Xbox Live Creators Program

Если вам нужно ознакомиться с разработки Xbox Live, Xbox Live Creators Program является хорошей отправной точкой для Xbox Live. Процесс утверждения, не от корпорации Майкрософт требуется присоединяться к этой программе и минимальной сертификации и требования к публикации.

Xbox Live Creators Program поддерживает только создание заголовков для [универсальной платформы Windows](https://msdn.microsoft.com/en-us/windows/uwp/get-started/universal-application-platform-guide) (UWP).  Эти заголовки, создаются как UWP игр, работы на компьютерах с Windows 10 и консолей Xbox One.  Дополнительные сведения о настройке UWP игры в службе Xbox One, см. в разделе [универсальной платформы Windows в службе Xbox One](https://docs.microsoft.com/en-us/windows/uwp/xbox-apps/index).  

В службе Xbox One, который предлагает игроками проверенный впечатления, игры, опубликованные через Xbox Live Creators Program будет продано в новый раздел коллекции Creators из Microsoft Store на Xbox. Это обеспечивает баланс между обеспечение открытую платформу, любой пользователь может разрабатывать и поставлять игры и консоли качества проверенный хранилища, которые игроками знать и ожидать, что. В Windows 10 название будет опубликован среди всех других Xbox Live игр в Microsoft Store.

### <a name="publishing-and-certification"></a>Публикация и сертификация
Вы должны быть зарегистрированы в [программе для разработчиков центра партнеров](https://developer.microsoft.com/store/register) для освобождения игры как часть Xbox Live Creators Program. Существует два набора требований, которым должны следовать игры:

1. Интегрируйте Xbox Live входа в систему и отобразить удостоверение пользователя (тег игрока, Gamerpic т. д.). Все остальные службы Xbox Live являются необязательными.
2. Следовать стандарту [политики Microsoft Store](https://msdn.microsoft.com/en-us/library/windows/apps/dn764944.aspx).

### <a name="supported-xbox-live-services"></a>Поддерживаемые службы Xbox Live
Заголовки, которые включены в Xbox Live Creators Program можно использовать списки лидеров, рекомендуемые Stats, название хранилища, подключенные хранилища и ограниченный набор возможностей социальных сетей. Достижений, online многопользовательскую и многие социальные функции являются **не** поддерживается, для которых в Xbox Live Creators Program.

Полный список поддерживаемых служб, см. в разделе [таблицы компонентов](#feature-table).

### <a name="supported-third-party-game-development-engines"></a>Поддерживаемое стороннее модули разработки игр
Xbox Live Creators Program заголовки являются игр на универсальной платформы Windows, которые можно создать, используя ряд популярных игровых движков. Корпорация Майкрософт предоставляет документацию для интеграции службы Xbox Live в UWP игр, созданных с помощью [игровое ядро Unity](https://unity.com). Можно найти [документации](get-started-with-creators/develop-creators-title-with-unity.md) детального рассмотрения Xbox Live интеграция с игры Unity, на этом сайте, а также скачайте и узнайте о построении Microsoft [подключаемый модуль Xbox Live Unity](https://github.com/Microsoft/xbox-live-unity-plugin).

Заголовки Xbox Live Creators Program также могут быть построены с игровых движков [конструкции (2 и 3)](https://www.scirra.com/construct2), и [GameMaker Studio 2](https://www.yoyogames.com/gamemaker). Оба игровых движков добавили поддержку Xbox Live, тем не менее, что поддержка обрабатывается игры ядра, создатели и не Корпорация Майкрософт. Дополнительные сведения и поддержка добавления Xbox Live в проект конструкция или GameMaker Studio 2 необходимо в документации каждого игровых движков соответственно.

[Узнайте, как интегрировать Xbox Live в проекте конструкции.](https://www.scirra.com/tutorials/9540/using-xbox-live-in-uwp-apps)

[Узнайте, как интегрировать Xbox Live в ваш проект GameMaker Studio 2.](https://www.yoyogames.com/gamemaker/xblc)

Для других механизмов разработки игр таких как [MonoGame](https://www.monogame.net/) или [Xenko](https://xenko.com/), который не имеют встроена в Xbox Live функциональные возможности или подключаемого модуля, по-прежнему можно использовать API-интерфейсы Xbox Live, добавляемый заголовок вашей Xbox Live. Чтобы использовать API-интерфейсы Xbox Live из вашего проекта, можно добавить ссылки на двоичные файлы с помощью пакетов NuGet или добавить источник API. Добавление пакетов NuGet ускоряет компиляцию, а добавление источника упрощает отладку.

### <a name="support-and-feedback"></a>Поддержка и отзывы
На любые вопросы можно ответить на [форумы MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=xboxlivedev).  Также можно задать вопросы программирования [Stack Overflow](https://stackoverflow.com/questions/tagged/xbox-live) с помощью тега «xbox live».  Группа Xbox Live будет взаимодействовать с сообществом и непрерывно улучшать наши API-интерфейсы, инструменты и документацию на основе полученных от вас отзывов.

Для разработчиков в Xbox Live Creators Program, вы можете [отправить новую идею](https://xbox.uservoice.com/forums/363186--new-ideas?category_id=196261) или [проголосовать за существующие идею](https://xbox.uservoice.com/forums/251649?category_id=210838) в наших [Xbox User Voice](https://xbox.uservoice.com/forums/363186--new-ideas).

## <a name="idxbox"></a>ID@Xbox

Xbox Live Creators Program отлично подходит для большого количества игры и разработчиков. Но если вы хотите получить доступ к полный стек Xbox Live, включая многопользовательскую online, достижений и игровые достижения, или вам требуется доступ к всю мощь Семейство устройств, применение пакетов разработки оборудования, основанных на Xbox One [ ID@Xbox ](https://www.xbox.com/en-US/developers/id) программы именно для вас.

Игры в ID@Xbox программы должен быть утвержден концепции и проходят через сертификации на Xbox One и Windows 10, который является больше времени обязательство со стороны пользователя.
ID@Xbox заголовки получить размещения в основной части Store, в сравнении с коллекцией Creators, который может обеспечить более расширенный доступ клиентам.

Разработчики в ID@Xbox программы также получить доступ к поддержки разработчиков и рекламные помощь от Майкрософт, а также полный набор частных технические документы и разработчик технических форумов. Вы можете продолжать использовать [форумы MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=xboxlivedev) или обратитесь за помощью связанные вопросы о программировании на [Stack Overflow](https://stackoverflow.com/questions/tagged/xbox-live) при необходимости с помощью тега «xbox live».

## <a name="microsoft-partners"></a>Партнеры корпорации Майкрософт

Разработчики, работающие с игр издателе, которая является партнером Майкрософт имеют доступ к полному набору возможностей Xbox Live и выделенной представители корпорации Майкрософт для помощи в разработке, сертификации и процесс выпуска.

## <a name="feature-table"></a>Таблицы компонентов

Иллюстрации функций, доступных для Xbox Live Creators Program, воспользуйтесь следующей таблицей и [ ID@Xbox ](https://www.xbox.com/en-US/developers/id) программы.  

<table>

<tr>
<th>Область функций</th>
<th>Функция</th>
<th>Описание</th>
<th> ID@Xbox </th>
<th>Программа Xbox Live Creators Program</th>
</tr>

<tr>
<td rowspan="2" class="dev-program-feature-name">Удостоверение</td>
<td>Вход или регистрации</td>
<td>Разрешить игроков войти в Xbox Live в заголовок вашей или при необходимости создайте новую учетную запись Xbox Live</td>
<td class="xbl-features-required">Обязательно</td>
<td class="xbl-features-required">Обязательно</td>
</tr>

<tr>
<td>Удостоверение пользователя</td>
<td>Использовать удостоверение Xbox Live, отображая: тег игрока, Gamerpic и т. д</td>
<td class="xbl-features-required">Обязательно</td>
<td class="xbl-features-required">Обязательно</td>
</tr>

<tr class="dev-program-feature-start">
<td rowspan="13" class="dev-program-feature-name">Социальные сети</td>

<td>Основные присутствия</td>
<td>Отображаемые строки основные присутствия, демонстрирующий активность пользователя в заголовок.  Например: «Стив воспроизводится Minecraft»</td>
<td class="xbl-features-automatic">Автоматически</td>
<td class="xbl-features-automatic">Автоматически</td>
</tr>

<tr>
<td>Недавно воспроизведенные</td>
<td>Отображаются в недавно воспроизведенных названия в приложение Xbox и Xbox One</td>
<td class="xbl-features-automatic">Автоматически</td>
<td class="xbl-features-automatic">Автоматически</td>
</tr>

<tr>
<td>Веб-канале активности</td>
<td>Появляются в действии, веб-канала в приложение Xbox и Xbox One</td>
<td class="xbl-features-automatic">Автоматически</td>
<td class="xbl-features-automatic">Автоматически</td>
</tr>

<tr>
<td>Раздел "Игры"</td>
<td>Есть связанные вместе с названием отображение статистики, видео и другого содержимого в веб-канале, относящиеся к название центр игры</td>
<td class="xbl-features-automatic">Автоматически</td>
<td class="xbl-features-automatic">Автоматически</td>
</tr>

<tr>
<td>Треф</td>
<td>Проигрыватели можно использовать приложение Xbox и Xbox One для создания клубы, можно при необходимости связать вместе с названием.</td>
<td class="xbl-features-automatic">Автоматически</td>
<td class="xbl-features-automatic">Автоматически</td>
</tr>

<tr>
<td>Поиск группы (LFG)</td>
<td>LFG позволяет игроков найти другие out игры для планирования многопользовательской игры.</td>
<td class="xbl-features-automatic">Автоматически</td>
<td class="xbl-features-automatic">Автоматически</td>
</tr>

<tr>
<td>GameDVR</td>
<td>Игрокам можно видеозапись свои сеансы игровой процесс и совместно использовать их на веб-канале активности.</td>
<td class="xbl-features-automatic">Автоматически</td>
<td class="xbl-features-automatic">Автоматически</td>
</tr>

<tr>
<td>Вещание</td>
<td>Игроки могут существовать свои сюжеты с помощью потоковой передачи служб, таких как Mixer и Twitch вещание</td>
<td class="xbl-features-automatic">Автоматически</td>
<td class="xbl-features-automatic">Автоматически</td>
</tr>

<tr>
<td>Полнофункциональное присутствие</td>
<td>Содержит более подробные сведения о игроков в вашей должности.  В то время как основные присутствия может показывать «Пользователя — в автомобиле Racing Game», широкие возможности присутствия позволяет указать дополнительные строки, например «пользователя способствует SuperCar в RainyForest»</td>
<td class="xbl-features-required">Обязательно</td>
<td class="xbl-features-notavailable">Не поддерживается</td>
</tr>

<tr>
<td>Друзья</td>
<td>Получить список друзей пользователя входа в систему для включения сценариев игру в социальных сетях в заголовок вашей.</td>
<td class="xbl-features-required">Обязательно</td>
<td class="xbl-features-limited">Необязательный / ограниченный (предоставляются только тех друзей, которые неудавшемся название)</td>
</tr>

<tr>
<td>Конфиденциальность</td>
<td>Разрешить игроков, чтобы отключить или заблокировать или другие игроки</td>
<td class="xbl-features-optional">Необязательный</td>
<td class="xbl-features-optional">Необязательный</td>
</tr>

<tr>
<td>Репутация</td>
<td>Игрокам получить или потере репутации через их поведения. Поведение используется в подбор игроков и может использоваться с названием особыми способами.</td>
<td class="xbl-features-optional">Необязательный</td>
<td class="xbl-features-notavailable">Не поддерживается</td>
</tr>

<tr>
<td>Диспетчер социальные сети</td>
<td>Эффективно извлекать сведения о игрока Социальный граф</td>
<td class="xbl-features-optional">Необязательный</td>
<td class="xbl-features-limited">Необязательный / ограниченный (предоставляются только тех друзей, которые неудавшемся название)</td>
</tr>

<tr class="dev-program-feature-start">
<td rowspan="4" class="dev-program-feature-name">Платформа данных</td>

<td>Показатели игроков</td>
<td>Отправьте статистические данные о проигрыватели, которые могут использоваться в списки лидеров.</td>
<td class="xbl-features-optional">Необязательный</td>
<td class="xbl-features-optional">Необязательно (только для данных платформы 2017 г.)</td>
</tr>

<tr>
<td>Избранные Stats</td>
<td>Назначить определенные stats «Рекомендуемые Stats», будут отображаться в центре игры.</td>
<td class="xbl-features-required">Обязательно</td>
<td class="xbl-features-optional">Необязательно (только для данных платформы 2017 г.)</td>
</tr>

<tr>
<td>Списки лидеров</td>
<td>Извлечения и отображения статистики проигрывателя в отсортированный способ способствуют конкуренции.</td>
<td class="xbl-features-optional">Необязательный</td>
<td class="xbl-features-optional">Необязательно (только для данных платформы 2017 г.)</td>
</tr>

<tr>
<td>Достижения с достижениями</td>
<td>Назначить определенные stats «Рекомендуемые Stats», будут отображаться в центре игры.</td>
<td class="xbl-features-required">Обязательно</td>
<td class="xbl-features-notavailable">Не поддерживается</td>
</tr>

<tr class="dev-program-feature-start">
<td rowspan="1" class="dev-program-feature-name">Media</td>

<td>Контекстный поиск</td>
<td>Добавление заметок GameDVR клипов с ключевыми словами, чтобы упростить игроки смогут найти клипы, что им нужно просмотреть соответствующий.</td>
<td class="xbl-features-optional">Необязательный</td>
<td class="xbl-features-notavailable">Не поддерживается</td>
</tr>


<tr class="dev-program-feature-start">
<td rowspan="2" class="dev-program-feature-name">Хранилище</td>

<td>Подключенное хранилище</td>
<td>Сохраняет перемещаемых игры в одной консоли Xbox и ПК</td>
<td class="xbl-features-required">Обязательно</td>
<td class="xbl-features-optional">Необязательный</td>
</tr>

<tr>
<td>Хранилище игр</td>
<td>Облачное хранилище для больших объемов пользователя или данные на заголовок.</td>
<td class="xbl-features-optional">Необязательный</td>
<td class="xbl-features-optional">Необязательный</td>
</tr>

<tr class="dev-program-feature-start">
<td rowspan="6" class="dev-program-feature-name">Online многопользовательскую</td>

<td>Каталог сеансов многопользовательской игры (MPSD)</td>
<td>Хранит сведения о многопользовательской, такие как список игроков, состояние и т. д.</td>
<td class="xbl-features-optional">Обязательно</td>
<td class="xbl-features-notavailable">Не поддерживается</td>
</tr>

<tr>
<td>Подбор игроков</td>
<td>Xbox Live может соответствовать игроки друг с другом для многопользовательской.</td>
<td class="xbl-features-optional">Необязательный</td>
<td class="xbl-features-notavailable">Не поддерживается</td>
</tr>

<tr>
<td>Арена</td>
<td>Стиль турнира игроки могут конкурировать друг с другом.</td>
<td class="xbl-features-optional">Необязательный</td>
<td class="xbl-features-notavailable">Не поддерживается</td>
</tr>

<tr>
<td>Чат, игры</td>
<td>Голосовой чат для игроков в игры</td>
<td class="xbl-features-optional">Необязательный</td>
<td class="xbl-features-notavailable">Не поддерживается</td>
</tr>

<tr>
<td>Xbox Live вычислений</td>
<td>Разверните исполняемые файлы и ресурсы, в которых название может взаимодействовать, можно перенести некоторые вычисления от клиента.</td>
<td class="xbl-features-optional">Необязательный</td>
<td class="xbl-features-notavailable">Не поддерживается</td>
</tr>

</table>
