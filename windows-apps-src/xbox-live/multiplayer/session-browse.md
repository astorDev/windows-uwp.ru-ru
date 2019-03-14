---
title: Обзор многопользовательской
description: Узнайте, как реализовать многопользовательской обзора с помощью Xbox Live многопользовательские.
ms.assetid: b4b3ed67-9e2c-4c14-9b27-083b8bccb3ce
ms.date: 10/16/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 579c71ef9266fb9a1ee4ef0538d1beffec0bb4ea
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57660679"
---
# <a name="multiplayer-session-browse"></a>Обзор многопользовательской

Многопользовательские обзора — это новая функция, появившаяся в ноябре 2016 сеанс, который включает заголовок запроса для получения списка открытых многопользовательские игры сеансов, соответствующих указанным критериям.

## <a name="what-is-session-browse"></a>Что такое обзор сеансов?

В случае просмотра сеанса проигрывателя в игре удалось извлечь список сеансов присоединяемая игр. Каждая запись сеанса в этот список содержит некоторые дополнительные метаданные о игры, которая проигрыватель можно использовать для их выберите Выбор сеансов для присоединения.  Они также можно фильтровать список сеансов на основе метаданных. Когда проигрыватель обнаруживает игр сеанса, который может понравиться их, они могут подключиться к сеансу.

Проигрыватель можно создать новый сеанс игр и используйте кнопку browse сеанса для привлечения дополнительных игроков, а не полагаться на подбор игроков.

Обзор сеансов отличается от традиционных координирующему сценарии в том, что проигрыватель самостоятельно выбирает какой игр сеанс, им необходимо присоединиться к в подбор игроков, обычно на игрок щелкнет кнопку «найти игру», которая пытается автоматически перевести проигрывателя в соответствующий сеанс игр. Несмотря на то Обзор сеансов вручную и медленнее процесс, который не всегда может выбирать объективно лучшие игры, он обеспечивает более точный контроль с проигрывателем и воспринимается как выбрать субъективному высококачественные игры.

Довольно часто для включения оба сценария сеанса обзора и подбор игроков в игры. Обычно координирующему используется для часто воспроизводиться игровых режимов во время сеанса просмотра используется для пользовательских игр.

**Пример:** Джон может заинтересовать воспроизведение Битва hero игры стиля области, но хочет играть в игры, в котором всех игроков случайным образом выбирал их hero. Он может получить список открытых сеансов игр и найти те, которые включают «случайных героев» в своем описании, или если это позволяет игр пользовательского интерфейса, он можно выбрать игрового режима «случайных hero» и получить только сеансы, которые помечены для указания, что они gam «RandomHero» ES.

Когда он находит игры, в которой он отметок "нравится", он присоединяется к игре. Когда достаточное количество людей подключились к сеансу, узел игр сеанса можно начать игру.

### <a name="roles"></a>Роли

Игры для обзора сеанса может потребоваться найма проигрывателей для конкретных ролей. Например проигрыватель может потребоваться создать игр сеанса, который указывает, что сеанс не более 5 атакует классы, но должна содержать по крайней мере 2 healer роли и роли по крайней мере 1 жидкости.

Когда другой проигрыватель применяет для сеанса, можно предварительно выбрать их роли и службы не дают возможность подключиться к сеансу в том случае, если нет открытых слотов для роли, которые они выбрали.

Другим примером может служить, если игрок хочет зарезервировать 2 слотов для своих друзей, для присоединения, игры можно указать роль «friends», а только проигрыватели, в которых дружественные с узла сеансов можно заполнить два разъема, выделенных в роль «friends».

Дополнительные сведения о ролях см. в разделе [многопользовательские ролей](multiplayer-roles.md).



## <a name="how-does-session-browse-work"></a>Как работает Обзор сеансов?

Обзор сеансов работает главным образом на использование дескрипторов поиска. Дескриптор поиска является пакет, содержащий ссылку на сеанс, а также дополнительные метаданные о сеансе, а именно атрибуты поиска данных.

Когда создает заголовок Обзор нового сеанса игры, допустимых для сеанса, он создает дескриптор поиска для сеанса. Дескриптор поиска хранится в многопользовательскую службы каталогов (MPSD), который поддерживает служба поиска обрабатывает для заголовка.

Если заголовок необходимо получить список сеансов, заголовок можно отправлять запрос поиска MPSD, который возвращает список дескрипторов поиска, которые соответствуют критериям поиска. Заголовок можно затем использовать список сеансов для отображения списка игр присоединяемую к исполнителю.

Когда сеанс заполнен, или в противном случае не может быть присоединен, заголовок можно удалить дескриптор поиска из MPSD таким образом, чтобы сеанс больше не будут отображаться в запросах обзора сеанса.

>[!NOTE]
> Служба поиска обрабатывает которые должны использоваться при отображении список сеансов, которые будут отображаться для пользователя. С помощью поиска обрабатывает для фона подбор игроков, является недопустимым и вместо этого рассмотрите возможность использования [SmartMatch](multiplayer-manager/play-multiplayer-with-matchmaking.md)

## <a name="set-up-a-session-for-session-browse"></a>Установка сеанса предложение for browse сеанса

Чтобы использовать маркеры поиска для сеанса, сеанс должен иметь следующие возможности задано значение true:

* `searchable`
* `userAuthorizationStyle`

>[!NOTE]
> `userAuthorizationStyle` Только возможности требуются для игр для универсальной платформы Windows, но мы рекомендуем реализовать их для всех Xbox Live игр, включая игры XDK, оно гарантирует, что будущие переносимости.

>[!NOTE]
> Установка `userAuthorizationStyle` значения по умолчанию возможность `readRestriction` и `joinRestriction` сеанса для `local` вместо `none`. Это означает, что заголовки следует использовать служба поиска обрабатывает или передачи дескрипторов присоединиться к сеансу игр.

Эти возможности можно задать в шаблоне сеанса при настройке службы Xbox Live.

Предложение FOR browse сеанса служба поиска обрабатывает необходимо создавать только о сеансах, которые будут использоваться для сценария игры, не для основной сеансов.

## <a name="what-does-it-mean-to-be-an-owner-of-a-session"></a>Что значит быть владельцем сеанса?

Хотя многие игры сеанса типов, таких как SmartMatch или только игры, друзья не требует владельца, для обзора сеансов сеанса вы можете иметь владельца. 

Наличие в сеансе, управляемая владельцем имеет некоторые преимущества для владельца. Владельцы можно удалить другие члены из сеанса, или изменить состояние владения других элементов.

Чтобы использовать владельцев для сеанса, сеанс должен иметь следующие возможности задано значение true:

* `hasOwners`

Если владельцем сеанса содержит член Xbox Live заблокирована, этот элемент не может подключиться к сеансу.

При использовании [многопользовательские ролей](multiplayer-roles.md), его можно задать, только для владельцев можно назначать роли пользователям.

Если все владельцы оставьте сеанс, а затем служба выполняет действие на основе сеанса `ownershipPolicy.migration` политики, который определен для данного сеанса. Если эта политика не «старой», проигрыватель, который находился в сеансе дольше всего устанавливается в качестве нового владельца. Если эта политика не «endsession» (по умолчанию, если не указано), служба завершает сеанс и удаляет все оставшиеся игроков из сеанса.


## <a name="search-handles"></a>Служба поиска обрабатывает

Дескриптор поиска, хранятся в MSPD в виде структуры JSON. В дополнение к содержит ссылку на сеанс, служба поиска обрабатывает также содержать дополнительные метаданные для поиска, известный как атрибуты поиска.

Сеанс может иметь только один дескриптор поиска, созданный для него в любое время.

Чтобы создать дескриптор поиска для сеанса в с помощью API-интерфейсы Xbox Live, сначала создайте `multiplayer::multiplayer_search_handle_request` , а затем передать этот объект для `multiplayer::multiplayer_service::set_search_handle()` метод.

### <a name="search-attributes"></a>Атрибуты поиска

Атрибуты поиска состоит из следующих компонентов:

`tags` -Теги — это дескрипторы строк, которые пользователи могут использовать для категоризации игр сеанса, аналогичную хэштег. Теги должны начинаться с буквы, не может содержать пробелы и должно быть не более 100 символов.
Примеры тегов: «ProRankOnly», «Norocketlaunchers», «cityMaps».

`strings` -Строки являются текстовые переменные и строковые имена должны начинаться с буквы, не может содержать пробелы и должно быть не более 100 символов.

Пример строки метаданных: ««=» Ножей оружие + pistols + rifles», «MapName» = «UrbanCityAssault», «Описание «=» готовность игру, Добро пожаловать новых людей».

`numbers` -Номеров числовых переменных, а число имен должно начинаться с буквы, не может содержать пробелы и должно быть не более 100 символов. API-интерфейсы Xbox Live извлекает значения как типа float.

Пример метаданных чисел: "MinLevel" = 25, "MaxRank" = 10.

>**Примечание.** Регистр букв тегов и строковые значения сохраняются в службе, но необходимо использовать функции tolower() при запросе для тегов. Это означает, что теги и строковых значений в настоящее время все обрабатываются как нижний регистр, даже если они содержат символы в верхнем регистре.

В Xbox Live API-интерфейсы, атрибуты поиска можно задать с помощью `set_tags()`, `set_stringsmetadata()`, и `set_numbers_metadata()` методы `multiplayer_search_handle_request` объекта.


### <a name="additional-details"></a>Дополнительные сведения

Когда вы получаете дескриптор поиска, результаты также включать дополнительные полезные данные о сеансе, таких как, если сеанс закрывается, существуют ли соединения ограничения на сеанс и т. д.

В Xbox Live API-интерфейсы, эти сведения, а также атрибуты поиска, включены в `multiplayer_search_handle_details` , возвращаются после поискового запроса.

### <a name="remove-a-search-handle"></a>Удалить дескриптор поиска

Если вы хотите удалить сеанс из обзора сеанса, например при заполнении, сеанса или закрытия сеанса, можно удалить дескриптор поиска.

В Xbox Live API-интерфейсы, можно использовать `multiplayer_service::clear_search_handle()` метод для удаления дескриптора поиска.

### <a name="example-create-a-search-handle-with-metadata"></a>Пример. Создать дескриптор поиска с метаданными

Ниже показано, как создать дескриптор поиска для сеанса с помощью API многопользовательские C++ Xbox Live.

```cpp
auto searchHandleReq = multiplayer_search_handle_request(sessionBrowseRef);

searchHandleReq.set_tags(std::vector<string_t> val);
searchHandleReq.set_numbers_metadata(std::unordered_map<string_t, double> metadata);
searchHandleReq.set_strings_metadata(std::unordered_map<string_t, string_t> metadata);

auto result = xboxLiveContext->multiplayer_service().set_search_handle(searchHandleReq)
.then([](xbox_live_result<void> result)
{
  if (result.err())
  {
    // handle error
  }
});
```


## <a name="create-a-search-query-for-sessions"></a>Создайте запрос поиска для сеансов

При получении списка дескрипторов поиска, можно использовать поисковый запрос для ограничения результатов к сеансам, соответствующих заданным критериям.

Синтаксис запроса поиска является [OData](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part2-url-conventions/odata-v4.0-errata02-os-part2-url-conventions-complete.html#_Toc406398092) стиль синтаксиса, с помощью поддерживаются только следующие операторы:

 Оператор | Описание
 --- | ---
 EQ | равно
 NE | Не равно
 gt | Больше
 GE | Больше или равно
 lt | Меньше
 LE | Меньше или равно
 и | Логическое и
 или | логического или (см. примечание ниже)

Можно также использовать лямбда-выражения и `tolower` каноническую функцию. Другие функции OData не поддерживаются.

При поиске тегов или строковых значений, необходимо использовать функцию «tolower» в запросе поиска, как только в настоящее время поддерживает служба поиска нижнего регистра строк.

Службы Xbox Live возвращает только первые 100 результатов, соответствующих поисковому запросу. Игра должна разрешать игроков уточнить их поисковый запрос, если результатов слишком широки.

>[!NOTE]
>  Логического OR поддерживаются в запросах строку фильтра; Однако только один или разрешена, он должен быть в корне вашего запроса. Не может иметь несколько OR в запросе, а также нельзя создать запрос, приведет к или не была в верхней части большинства уровне структуры запроса.

### <a name="search-handle-query-examples"></a>Примеры запросов поиска дескриптор

В вызове restful «Фильтр» является следует ввести строку языка фильтра OData, которая будет запускаться в запрос на все дескрипторы поиска.  
В многопользовательской API 2015, можно указать строка фильтра поиска в *searchFilter* параметр `multiplayer_service.get_search_handles()` метод.  

В настоящее время поддерживаются следующие сценарии фильтра:

 Фильтровать по | Строка фильтра поиска
 --- | ---
 Один член xuid "1234566" | «сеанс/memberXuids/any(d:d eq '1234566')»
 Один владелец xuid "1234566" | «сеанс/ownerXuids/any(d:d eq '1234566')»
 Строка «forzacarclass» равным «classb» | «tolower(strings/forzacarclass) eq «класса» б»
 Число «forzaskill» равен 6 | «номера/forzaskill eq 6»
 Число «halokdratio» больше, чем 1.5 | «gt номера/halokdratio 1.5»
 Тег «coolpeopleonly» | "tags/any(d:tolower(d) eq 'coolpeopleonly')"
 Сеансы, которые не содержат тег «cursingallowed» | "tags/any(d:tolower(d) ne"cursingallowed")»
 Сеансы, которые не содержат «rank», число равно 0 | «номера или рейтинга ne 0»
 Сеансы, которые не содержат строку «forzacarclass» равен «classa» | «ne tolower(strings/forzacarclass) «класса» а»
 Тег «coolpeopleonly» и «halokdratio» равным 7.5 номер | "tags/any(d:tolower(d) eq"coolpeopleonly") eq true, а также номера/halokdratio eq 7.5»
 Число «halodkratio» больше или равно 1.5, число «rank» меньше 60, а также номера «customnumbervalue» меньше или равно 5 | «номера/halokdratio ge 1.5 и номера или рейтинга lt 60 и номера/customnumbervalue le 5»
 Идентификатор награду "123456" | "achievementIds/any(d:d eq '123456')"
 Код языка «en» | «eq языка «en»»
 Запланированное время, возвращает все запланированные раз меньше или равно указанного времени | «сеанс/scheduledTime le "2009-06-15T13:45:30.0900000Z"»
 Переданный время, возвращает все учтена время меньше, чем указанное время | «сеанс/postedTime lt "2009-06-15T13:45:30.0900000Z"»
 Состояние регистрации сеанса | «сеанс "/" registrationState «зарегистрировано» eq»
 Где число элементов сеанса равно 5 | «сеанс/membersCount eq 5»
 Где число целевых элементов сеанса больше, чем 1 | «gt сеанса/targetMembersCount 1»
 Где максимальное количество элементов сеансов — меньше 3 | «сеанс/maxMembersCount lt 3»
 Разница между числом целевых элементов сеанса и число сеансов элементов которого меньше или равно 5 | «le сеанса/targetMembersCountRemaining 5»
 Где разницу между максимальное количество элементов сеанса и номер сеанса элементов больше, чем 2 | «gt сеанса/maxMembersCountRemaining 2»
 Разница между числом целевых элементов сеанса и количество элементов сеанса которого меньше или равно 15.</br> Если роль не имеет указанный целевой объект, этот запрос фильтрует от разницу между максимальное количество элементов сеанса и количество элементов сеанса. | «сеанс/должен le 15»
 Роль «подтверждено» из типа роли «lfg», где число элементов с этой ролью равно 5 | «/ роли/lfg/подтверждена и число сеансов eq 5»
 Роль «подтверждено» из типа роли «lfg», где целевой объект этой роли больше 1.</br> Если роль не имеет цель, указанную, max роли используется вместо этого. | «gt сеанса/роли/lfg/подтверждена/Целевая рабочая 1»
 Роль «подтверждено» роли типа «lfg», где разницу между целевой роли и количество элементов с этой роли — меньше или равно 15.</br> Если роль не имеет указанный целевой объект, этот запрос фильтрует от разницу между max роли и количество элементов с этой роли. | «le/роли/lfg/подтверждена/потребности в сеансах 15»
 Все совпадения при поиске дескрипторов, которые указывают на сеанс, содержащий определенное ключевое слово | "session/keywords/any(d:tolower(d) eq 'level2')"
 Все совпадения при поиске дескрипторов, которые указывают на сеанс, принадлежащих определенной scid | «сеанс/scid eq "151512315"»
 Все совпадения при поиске дескрипторов, которые указывают на сеансе, который использует имя конкретного шаблона | "session/templateName eq 'mytemplate1'"
 Все совпадения при поиске дескрипторов, которые имеют тег «elite» или «гнуть» больше 15 номер и строка «хронологией» равным «фиолетовым» | "tags/any(a:tolower(a) eq"elite") или номер/гнуть gt 15 и строка/хронологией eq «фиолетовым»»

### <a name="refreshing-search-results"></a>Обновление результатов поиска

 Игры следует избежать автоматическое обновление список сеансов, но вместо предоставляют пользовательский Интерфейс, который позволяет проигрывателя, чтобы вручную обновить список (возможно, после уточнения критерии поиска, чтобы лучше фильтрации результатов).

 Если игрок пытается присоединиться к сеансу, но этот сеанс — полный или закрытых, игры следует обновить результатами поиска.

 Слишком много поиска обновлений может привести к регулирования службы, поэтому название следует ограничить скорость, с которой запрос может быть обновлен.

 Чтобы уменьшить количество вызовов службы, служба поиска обрабатывает включают свойства пользовательского сеанса, которые можно использовать для хранения и запросов быстро меняющимися атрибуты сеанса. Такие атрибуты не должны храниться в атрибутах поиска.

### <a name="example-query-for-search-handles"></a>Пример: запрос для поиска обрабатывает

 Приведенный ниже показано, как запрашивать маркеры поиска. API возвращает коллекцию `multiplayer_search_handle_details` объекты, представляющие все маркеры поиска, соответствующих запросу.

```cpp
 auto result = multiplayer_service().get_search_handles(scid, template, orderBy, orderAscending, searchFilter)
 .then([](xbox_live_result<std::vector<multiplayer_search_handle_details>> result)
 {
   if (result.err())
   {
      // handle error
   }
   else
   {
      // parse result.payload
   }
 });

 /* Payload element properties

 multiplayer_search_handle_details
 {
   string_t& handle_id();
   multiplayer_session_reference& session_reference();
   std::vector<string_t>& session_owner_xbox_user_ids();
   std::vector<string_t>& tags();
   std::unordered_map<string_t, double>& numbers_metadata();
   std::unordered_map<string_t, string_t>& strings_metadata();
   std::unordered_map<string_t, multiplayer_role_type>& role_types();
 }
 */
```

## <a name="join-a-session-by-using-a-search-handle"></a>Присоединиться к сеансу с помощью дескриптора поиска

После получения дескриптора поиска для сеанса, который требуется присоединить, следует использовать заголовок `MultiplayerService::WriteSessionByHandleAsync()` или `multiplayer_service::write_session_by_handle()` добавлять себя в сеанс.

> [!NOTE]
> `WriteSessionAsync()` И `write_session()` методы нельзя подключиться к сеансу обзора сеанса.

Ниже показано, как присоединиться к сеансу после получения дескриптора поиска.

```cpp
void Sample::BrowseSearchHandles()
{
    auto context = m_liveResources->GetLiveContext();
    context->multiplayer_service().get_search_handles(...)
    .then([this](xbox_live_result<std::vector<multiplayer_search_handle_details>> searchHandles)
    {
        if (searchHandles.err())
        {
            LogErrorFormat( L"BrowseSearchHandles failed: %S\n", searchHandles.err_message().c_str() );
        }
        else
        {
            m_searchHandles = searchHandles.payload();

            // Join the game session

            auto handleId = m_searchHandles.at(0).handle_id();
            auto sessionRef = multiplayer_session_reference(m_searchHandles.at(0).session_reference());
            auto gameSession = std::make_shared<multiplayer_session>(m_liveResources->GetLiveContext()->xbox_live_user_id(), sessionRef);
            gameSession->join(web::json::value::null(), false, false, false);

            context->multiplayer_service().write_session_by_handle(gameSession, multiplayer_session_write_mode::update_existing, handleId)
            .then([this, sessionRef](xbox_live_result<std::shared_ptr<multiplayer_session>> writeResult)
            {
                if (!writeResult.err())
                {
                    // Join the game session via MPM
                    m_multiplayerManager->join_game(sessionRef.session_name(), sessionRef.session_template_name());
                }
            });
        }
    });
}
```