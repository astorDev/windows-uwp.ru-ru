---
ms.assetid: 2f76c520-84a3-4066-8eb3-ecc0ecd198a7
title: Тесты приложения, перенесенного из классического приложения для Windows
description: Используйте встроенные тесты моста для классических приложений, чтобы гарантировать оптимизацию классического приложения для преобразования в приложение UWP.
ms.date: 12/18/2017
ms.topic: article
keywords: windows 10, uwp, сертификация приложений
ms.localizationpriority: medium
ms.openlocfilehash: 37c382fb81a4527b730840142643ff72b9020127
ms.sourcegitcommit: ef723e3d6b1b67213c78da696838a920c66d5d30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2020
ms.locfileid: "82730295"
---
# <a name="windows-desktop-bridge-app-tests"></a>Тесты приложения, перенесенного из классического приложения для Windows

[Приложения, созданные с помощью моста для классических приложений,](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-root) — это классические приложения для Windows, преобразованные в приложение универсальной платформы Windows (UWP) с использованием [моста для классических приложений](https://developer.microsoft.com/windows/bridges/desktop). После преобразования классическое приложение для Windows упаковывается, обслуживается и развертывается в виде пакета приложения UWP (APPX или APPXBUNDLE), предназначенного для Windows 10 Desktop.

## <a name="required-versus-optional-tests"></a>Обязательные и необязательные тесты
Для приложений, созданный с помощью моста для классических приложений, существуют необязательные тесты, которые носят исключительно информационный характер и не будут использоваться для оценки приложения при выставлении в Microsoft Store. Мы рекомендуем изучать результаты этих тестов для повышения качества приложений. Общие критерии допуска приложения к выставлению в Store определяются обязательными тестами, а результаты необязательных тестов при этом не учитываются.

## <a name="current-optional-tests"></a>Существующие необязательные тесты

### <a name="1-digitally-signed-file-test"></a>1. Тест файла с цифровой подписью. 
**Фон**  
Этот тест позволяет убедиться, что все переносимые исполняемые файлы содержат действительную подпись. Наличие файлов с цифровой подписью указывает на то, что программное обеспечение является подлинным.

**Сведения о тесте**  
При выполнении теста просматриваются все переносимые исполняемые файлы в пакете, а их заголовки проверяются на наличие подписи. Мы рекомендуем, чтобы все переносимые исполняемые файлы имели цифровую подпись. Если какой-либо переносимый исполняемый файл не подписан, будет создано предупреждение.
 
**Корректирующие действия**  
Рекомендуется, чтобы в пакете всегда содержались файлы с цифровой подписью. Дополнительные сведения см. в разделе [Знакомство с подписыванием кода](https://docs.microsoft.com/previous-versions/windows/internet-explorer/ie-developer/platform-apis/ms537361(v=vs.85)).

### <a name="2-file-association-verbs"></a>2. Команды сопоставления файлов 
**Фон**  
При выполнении этого теста просматривается реестр пакета и проверяется, зарегистрированы ли в нем команды сопоставления файлов. 

**Сведения о тесте**  
Вы можете расширить возможности преобразованных классических приложений с помощью разных API среды выполнения Windows. Этот тест позволяет убедиться, что двоичные файлы UWP в приложении не вызывают API, не относящиеся к среде выполнения Windows. Для двоичных файлов UWP установлен флаг **AppContainer**.

**Корректирующие действия**  
См. статью [Мост для перехода от классических приложений к UWP: расширение приложений](https://docs.microsoft.com/windows/apps/desktop/modernize/desktop-to-uwp-extensions), где приводится объяснение этих расширений и их правильного использования. 

### <a name="3-debug-configuration-test"></a>3. Тест отладочной конфигурации
Этот тест позволяет проверить, не является ли MSIX- или APPX-файл отладочной сборкой.
 
**Фон**  
Чтобы пройти сертификацию для Microsoft Store, приложения не должны быть скомпилированы для отладки и не должны ссылаться на отладочные версии исполняемого файла. Для прохождения этого теста вам также необходимо создать для своего приложения собственный оптимизированный код.
 
**Сведения о тесте**  
Проверьте приложение и убедитесь, что это не отладочная сборка и что приложение не связано с отладочными платформами.
 
**Корректирующие действия**  
* Выполните сборку рабочего выпуска приложения, прежде чем отправлять его в Microsoft Store.
* Убедитесь, что у вас установлена правильная версия платформы .NET Framework.
* Убедитесь, что приложение не содержит ссылок на отладочные версии платформы, а его сборка выполняется с использованием окончательной версии. Если приложение содержит компоненты .NET, убедитесь, что у вас установлена правильная версия платформы .NET Framework.

### <a name="4-package-sanity-test"></a>4. Проверки работоспособности пакета
#### <a name="41-archive-files-usage"></a>4.1 Использование архивных файлов

**Фон**  
Этот тест помогает создавать более качественные приложения, выполняемые на устройствах с [Windows 10 S](https://www.microsoft.com/windows/windows-10-s), с помощью моста для классических приложений.

**Сведения о тесте**  
Проверяются все исполняемые файлы внутри архивных файлов или самоизвлекающегося содержимого. Поскольку исполняемые файлы внутри этого типа содержимого не подписываются во время регистрации в Microsoft Store, в системах Windows 10 S приложение может работать не так, как ожидается.
 
**Корректирующие действия**
* Необходимо оценить файлы, отмеченные в ходе проверки, чтобы определить, влияют ли они на ваше приложение, выполняемое в среде Windows 10 S.
* Если они влияют на ваше приложение, удалите исполняемые файлы из архивных и не используйте самоизвлекающиеся архивы для размещения исполняемых файлов на диске. Это позволит сохранить функциональность приложения.

#### <a name="42-blocked-executables"></a>4.2 Заблокированные исполняемые файлы

**Фон**  
Этот тест помогает создавать более качественные приложения, выполняемые на устройствах с [Windows 10 S](https://www.microsoft.com/windows/windows-10-s), с помощью моста для классических приложений. 

**Сведения о тесте**  
Проверяется, пытается ли приложение запустить исполняемые файлы (что запрещено в системах Windows 10 S). Приложения, которые используют эту возможность, могут выполняться в системах Windows 10 S не так, как ожидается. 

**Корректирующие действия**  
* Определите, какие из записей, отмеченных в ходе проверки, представляют собой вызов запуска исполняемого файла, который не является частью вашего приложения, и удалите эти вызовы. 
* Если отмеченные файлы являются частью вашего приложения, предупреждение можно игнорировать.


## <a name="current-required-tests"></a>Существующие обязательные тесты

### <a name="1-app-capabilities-test-special-use-capabilities"></a>1. Тест возможностей приложения (специальные возможности)

**Фон**  
Специальные возможности предназначены для особых сценариев. Использовать их разрешается только учетным записям компании. 

**Сведения о тесте**  
Проверяет, объявляет ли приложение какие-либо из следующих возможностей: 
* EnterpriseAuthentication
* SharedUserCertificates
* DocumentsLibrary

Если объявлены какие-либо из этих возможностей, в результате теста выводится предупреждение для пользователя. 

**Корректирующие действия**  
Рекомендуется удалить специальные возможности, если они не требуются для приложения. Кроме того, согласно политике размещения приложений использование этих возможностей дополнительно анализируется при размещении.

### <a name="2-app-manifest-resources-tests"></a>2. Проверка ресурсов манифеста приложения 
#### <a name="21-app-resources-validation"></a>2.1 Проверка ресурсов приложения
Приложение может не установиться надлежащим образом, если в манифесте приложения объявлены неправильные строки или изображения. Если при установке приложения возникают такие ошибки, логотип приложения или другие изображения могут отображаться неправильно.    

**Сведения о тесте**  
Проверяются ресурсы, определенные в манифесте приложения, чтобы убедиться, что они имеются и действительны.

**Корректирующее действие**  
Руководствуйтесь следующей таблицей.

Сообщение об ошибке | Комментарии
--------------|---------
Изображение {имя изображения} определяет квалификаторы Scale и TargetSize; единовременно можно задать только один квалификатор. | Вы можете настроить изображения для различных разрешений. В фактическом сообщении {image name} содержит имя изображения с ошибкой. Убедитесь, что каждое изображение определяет в качестве квалификатора Scale или TargetSize. 
Изображение {image name} не соответствует ограничениям по размеру.  | Убедитесь, что все изображения приложения соответствуют ограничениям по размеру. В фактическом сообщении {image name} содержит имя изображения с ошибкой. 
Изображение {image name} в пакете отсутствует.  | Отсутствует необходимое изображение. В фактическом сообщении {image name} содержит имя отсутствующего изображения. 
Изображение {image name} не является действительным файлом изображения.  | Убедитесь, что все изображения приложения соответствуют требованиям к ограничению форматов файлов. В фактическом сообщении {image name} содержит недопустимое имя изображения. 
Изображение "BadgeLogo" имеет значение ABGR {value} в положении (x, y), которое является недействительным. Пиксель должен быть белым (##FFFFFF) или прозрачным (00######)  | Индикатор событий (BadgeLogo) — это изображение рядом с уведомлением на индикаторе событий для идентификации приложения на экране блокировки. Это изображение должно быть монохромным, то есть содержать только белые и прозрачные пиксели. В фактическом сообщении {value} содержит недопустимое значение цвета изображения. 
Изображение "BadgeLogo" имеет значение ABGR {value} в положении (x, y), которое является недопустимым для белого изображения высокой контрастности. Пиксель должен быть цвета ##2A2A2A или темнее либо прозрачным (00######).  | Индикатор событий (BadgeLogo) — это изображение рядом с уведомлением на индикаторе событий для идентификации приложения на экране блокировки. Так как в режиме светлой схемы с высокой контрастностью логотип отображается на белом фоне, нужно использовать темную версию логотипа. Белое изображение индикатора событий высокой контрастности может содержать только пиксели темнее чем (##2A2A2A) или прозрачные пиксели. В фактическом сообщении {value} содержит недопустимое значение цвета изображения. 
Изображение должно определять как минимум один вариант без квалификатора TargetSize. Оно должно определять квалификатор Scale или оставлять квалификаторы Scale и TargetSize неопределенными, что по умолчанию означает значение Scale-100.  | Дополнительные сведения см. в руководствах по [адаптивному дизайну](https://docs.microsoft.com/windows/uwp/layout/screen-sizes-and-breakpoints-for-responsive-design) и [ресурсам приложения](https://docs.microsoft.com/windows/uwp/design/app-settings/store-and-retrieve-app-data). 
В пакете отсутствует файл resources.pri.  | Если в манифесте вашего приложения есть локализуемое содержимое, убедитесь, что пакет вашего приложения содержит допустимый файл resources.pri. 
Файл resources.pri должен содержать карту ресурсов с именем, которое соответствует имени пакета {package full name}.  | Вы можете увидеть эту ошибку, если манифест изменился, а имя карты ресурсов в файле resources.pri не соответствует имени пакета в манифесте. В фактическом сообщении {package full name} содержит имя пакета, которое должно быть указано в файле resources.pri. Чтобы устранить ошибку, необходимо перестроить файл resources.pri, причем самым простым способом может стать перестройка пакета приложения. 
Для файла "resources.pri" нельзя включать параметр AutoMerge.  | MakePRI.exe поддерживает параметр с названием AutoMerge. По умолчанию параметр AutoMerge отключен. Когда параметр AutoMerge включен, ресурсы языкового пакета приложения объединяются в единый файл resources.pri во время выполнения. Мы не рекомендуем использовать этот параметр в приложениях, предназначенных для распространения через Microsoft Store. Файл resources.pri приложения, распространяемого через Microsoft Store, должен находиться в корневом каталоге пакета приложения и содержать все ссылки на языковые пакеты, поддерживаемые приложением. 
Строка {string} не удовлетворяет ограничению по длине в {number} символов.  | См. раздел [Требования к пакетам приложения](https://docs.microsoft.com/windows/uwp/publish/app-package-requirements). В фактическом сообщении строка {string} заменяется строкой с ошибкой, а {number} — это максимальная длина. 
Строка {string} не должна начинаться или заканчиваться пробелами.  | Схема элементов в манифесте приложения не разрешает использование символов пробела в начале и конце строки. В фактическом сообщении строка {string} заменяется строкой с ошибкой. Убедитесь, что локализованные значения полей манифеста в файле resources.pri не начинаются и не заканчиваются пробелами. 
Строка должна быть непустой (длина больше нуля)  | Дополнительные сведения см. в разделе [Требования к пакетам приложения](https://docs.microsoft.com/windows/uwp/publish/app-package-requirements). 
В файле resources.pri не указываются ресурсы по умолчанию.  | Дополнительные сведения см. в руководстве по [ресурсам приложений](https://docs.microsoft.com/windows/uwp/design/app-settings/store-and-retrieve-app-data). В конфигурации сборки по умолчанию при генерации наборов Visual Studio включает в пакет приложения только ресурсы изображений scale-200. Другие ресурсы помещаются в пакет ресурсов. Вам необходимо либо включить ресурсы изображений scale-200, либо настроить проект таким образом, чтобы он включал имеющиеся ресурсы. 
В файле "resources.pri" не указывается значение ресурса.  | Убедитесь, что манифест приложения содержит допустимые ресурсы, определенные в файле resources.pri. 
Размер файла изображения {filename} должен быть меньше 204 800 байт.  | Уменьшите размер указанных изображений. 
Файл {filename} не должен содержать раздел обратного сопоставления.  | Обратное сопоставление создается в ходе "отладки F5" в Visual Studio при вызове в makepri.exe, но его можно удалить, выполнив makepri.exe без параметра /m при создании PRI-файла. 



#### <a name="22-branding-validation"></a>2.2 Проверка фирменной символики
**Фон**  
Приложения, созданные с помощью моста для классических приложений, должны быть завершенными и полностью работоспособными. Приложения, использующие изображения по умолчанию (из шаблонов или примеров SDK), непривлекательны для пользователей, и их сложно найти в каталоге Магазина.

**Сведения о тесте**  
Тест позволяет проверить, не использует ли приложение изображения по умолчанию — из примеров SDK или из Visual Studio. 

**Корректирующие действия**  
Замените изображения по умолчанию индивидуальными изображениями, отражающими суть приложения.

### <a name="3-package-compliance-tests"></a>3. Тесты на соответствие пакета
#### <a name="31-app-manifest"></a>3.1 Манифест приложения
Проверяет содержание манифеста приложения, чтобы убедиться в его правильности.

**Фон**  
Приложения должны иметь правильно отформатированный манифест.

**Сведения о тесте**  
Проверяет манифест приложения, чтобы подтвердить правильность его содержимого в соответствии с разделом [Требования к пакетам приложений](https://docs.microsoft.com/windows/uwp/publish/app-package-requirements). В этом тесте выполняются описанные ниже проверки.
* **Расширения файлов и протоколы**  
Приложение может объявлять типы файлов, с которыми оно может быть сопоставлено. Объявление большого количества редких типов файлов ухудшает взаимодействие с пользователем. Этот тест позволяет ограничить количество расширений файлов, которые можно сопоставить с приложением.
* **Правило зависимости платформы**  
В этом тесте реализуется требование того, чтобы приложения объявляли надлежащие зависимости для UWP. При ошибочной зависимости тест не будет пройден. При выявлении несоответствия между версией ОС, в которой должно выполняться приложение, и заданными в нем зависимостями платформы тест считается непройденным. Тест также завершается ошибкой, если приложение ссылается на любую из предварительных версий библиотек DLL платформы.
* **Проверка межпроцессного взаимодействия**  
В этом тесте реализуется требование, запрещающее приложениям, созданным с помощью моста для классических приложений, передавать данные за пределы контейнера приложения компонентам рабочего стола. Межпроцессное взаимодействие предназначено только для параллельно загружаемых приложений. Приложения, в которых задан атрибут [**ActivatableClassAttribute**](https://docs.microsoft.com/uwp/schemas/appxpackage/appxmanifestschema/element-activatableclassattribute) с именем `DesktopApplicationPath`, не пройдут этот тест.  

**Корректирующее действие**  
Проверьте манифест приложения на соответствие требованиям, описанным в разделе [Требования к пакетам приложений](https://docs.microsoft.com/windows/uwp/publish/app-package-requirements).


#### <a name="32-application-count"></a>3.2 Число приложений
Этот тест позволяет убедиться, что пакет приложений (например, APPX) содержит одно приложение. 

**Фон**  
Тест реализован согласно политике Microsoft Store. 

**Сведения о тесте**  
Этот тест позволяет убедиться в том, что общее количество пакетов APPX в наборе меньше 512 и в наборе есть только один "основной" пакет. В ходе теста также проверяется, имеет ли номер версии набора значение 0. 

**Корректирующие действия**  
Убедитесь, что пакет приложения и набор соответствуют требованиям, приведенным в разделе **Сведения о тесте**.


#### <a name="33-registry-checks"></a>3.3 Проверки реестра
**Фон**  
Этот тест позволяет проверить, устанавливает и обновляет ли приложение какие-либо новые службы или драйверы.

**Сведения о тесте**  
Тест позволяет проверить содержимое файла registry.dat на наличие обновлений определенных разделов реестра, указывающих на регистрацию новой службы или драйвера. Если приложение пытается установить драйвер или службу, тест не будет пройден.  

**Корректирующие действия**  
Просмотрите ошибки и удалите соответствующие службы или драйверы, если они не нужны. Если приложение зависит от них, необходимо изменить такое приложение, чтобы зарегистрировать его в Store.


### <a name="4-platform-appropriate-files-test"></a>4. Тест соответствия файлов платформе
Приложения, устанавливающие смешанные двоичные файлы, могут аварийно завершаться или работать неправильно в зависимости от архитектуры процессора пользователя. 

**Фон**  
Этот тест позволяет проверить двоичные файлы в пакете приложения на предмет конфликтов с архитектурой. Пакет приложения не должен включать двоичные файлы, которые нельзя использовать в системе с архитектурой процессора, указанной в манифесте. Это может привести к сбою приложения или излишнему увеличению размера его пакета. 

**Сведения о тесте**  
Проверяет соответствие разрядности для каждого файла в заголовке PE архитектуре процессора, объявленной в пакете приложения. 

**Корректирующие действия**  
Следуя нижеприведенным рекомендациям, вы гарантируете, что в пакет вашего приложения войдут только те файлы, которые поддерживаются архитектурой, указанной в манифесте приложения. 
* Если целевая архитектура процессора для приложения не зависит от типа процессора, то пакет приложения не должен включать двоичные файлы и файлы изображений типов x86, x64 и ARM.
* Если в качестве целевой архитектуры процессора для приложения выбрана архитектура x86, то пакет приложения должен включать только двоичные файлы и файлы изображений типа x86. Если пакет содержит двоичные файлы или файлы изображений типов x64 или ARM, он не пройдет тестирование.
* Если в качестве целевой архитектуры процессора для приложения выбрана архитектура x64, то пакет приложения должен включать двоичные файлы и файлы изображений типа x64. В этом случае пакет также может включать файлы типа x86, но основные элементы взаимодействия с приложением должны использовать двоичные файлы типа x64. Если пакет содержит двоичные файлы или файлы изображений типа ARM либо двоичные файлы или файлы изображений *только* типа x86, он не пройдет тест.
* Если в качестве целевой архитектуры процессора для приложения выбрана архитектура ARM, то пакет приложения должен включать только двоичные файлы и файлы изображений типа ARM. Если пакет содержит двоичные файлы или файлы изображений типов x64 или x86, он не пройдет тест. 

### <a name="5-supported-api-test"></a>5. Проверка API
Позволяет убедиться, что в приложении не используются несовместимые API. 

**Фон**  
Приложения, созданные с помощью моста для классических приложений, могут использовать некоторые устаревшие API Win32 вместе с современными API (компонентами UWP). Этот тест позволяет выявить управляемые двоичные файлы, которые используют неподдерживаемые API.
 
**Сведения о тесте**  
Этот тест позволяет проверить все компоненты UWP в приложении следующим образом:
* Позволяет убедиться, что ни один управляемый двоичный файл в пакете приложения не использует зависимость от API Win32, который не поддерживается для разработки приложений UWP. Для этого проверяется таблица адресов импорта для двоичного файла.
* Также позволяет убедиться, что ни один управляемый двоичный файл в пакете приложения не использует зависимость от функции вне утвержденного профиля. 

**Корректирующие действия**  
Это можно исправить, выполнив компиляцию приложения в виде сборки выпуска, а не отладочной сборки. 

> [!NOTE]
> Отладочная сборка приложения не пройдет этот тест, даже если в приложении используются только [API для приложений UWP](https://docs.microsoft.com/uwp/). Проверьте сообщения об ошибках, чтобы выявить имеющийся API, который не разрешен для приложений UWP. 

> [!NOTE]
> Приложения на C++ в конфигурации отладки не пройдут этот тест, даже если в конфигурации используются только API из пакета Windows SDK для приложений UWP. Дополнительные сведения об этом см. в статье [Альтернативы API Windows в приложениях UWP](https://docs.microsoft.com/uwp/win32-and-com/win32-and-com-for-uwp-apps).

### <a name="6-user-account-control-uac-test"></a>6. Тест контроля учетных записей (UAC)  

**Фон**  
Позволяет убедиться, что приложение не запрашивает контроль учетных записей во время выполнения.

**Сведения о тесте**  
Приложение не может запрашивать повышение прав администратора или доступ UIAccess согласно политике Microsoft Store. Повышение разрешений безопасности не поддерживается. 

**Корректирующие действия**  
Приложения должны запускаться от имени текущего пользователя. Подробные сведения см. в статье [Обзор системы безопасности модели автоматизации пользовательского интерфейса](https://docs.microsoft.com/dotnet/framework/ui-automation/ui-automation-security-overview?redirectedfrom=MSDN).

 
### <a name="7-windows-runtime-metadata-validation"></a>7. Проверка метаданных среды выполнения Windows
**Фон**  
Позволяет убедиться, что компоненты, предоставляемые в приложении, соответствуют системе типов универсальной платформы Windows (UWP).

**Сведения о тесте**  
В ходе этого теста возвращается ряд флагов, связанных с надлежащим использованием типов.

**Корректирующие действия**  
* **Атрибут ExclusiveTo**  
Убедитесь, что классы UWP не реализуют интерфейсы, отмеченные как ExclusiveTo для другого класса.
* **Правильность общих метаданных**  
Убедитесь, что компилятор, который вы применяете для создания типов, использует последнюю версию спецификаций UWP.
* **Свойства**  
Убедитесь, что для всех свойств в классе UWP имеется метод `get` (методы `set`необязательны). Убедитесь, что тип, возвращаемый методом `get`, совпадает с типом входного параметра метода `set`.
* **Расположение типов**  
Убедитесь, что метаданные для всех типов UWP находятся в WINMD-файле с самым длинным именем, соответствующим пространству имен, в пакете приложения.
* **Чувствительность к регистру имени типа**  
Убедитесь, что в пакете приложения все типы UWP имеют уникальные имена без учета регистра. Также убедитесь, что ни одно имя типа UWP не используется одновременно в качестве имени пространства имен в пакете приложения.
* **Правильность имен типов**  
Убедитесь, что в глобальном пространстве имен или в пространстве имен Windows верхнего уровня отсутствуют типы UWP.
 

### <a name="8-windows-security-features-tests"></a>8. Проверка средств безопасности Windows
Изменение используемых по умолчанию защитных средств Windows может подвергнуть пользователей повышенному риску. 

#### <a name="81-banned-file-analyzer"></a>8.1 Анализатор запрещенных файлов
**Фон**  
Для некоторых файлов улучшена безопасность, надежность и внесены другие важные улучшения. Приложения, созданные с помощью моста для классических приложений, должны содержать последние версии этих файлов, так как устаревшие версии представляют угрозу. Комплект сертификации приложений для Windows блокирует эти файлы, чтобы все приложения гарантированно пользовались текущей версией.

**Сведения о тесте**  
На данный момент в комплекте сертификации приложений для Windows проверяется наличие следующих запрещенных файлов:
* *Bing.Maps.JavaScript\js\veapicore.js*  
Обычно этот тест не удается пройти, если приложение использует версию файла "Release Preview" вместо последнего официального выпуска. 

**Корректирующие действия**  
Используйте последнюю версию [пакета SDK Карт Bing](https://www.bingmapsportal.com/) для приложений UWP.

#### <a name="82-private-code-signing"></a>8.2 Подписывание частного кода
Проверяет наличие двоичных файлов для подписывания частного кода в пакете приложения. 

**Фон**  
Файлы подписывания частного кода должны оставаться недоступными, поскольку в случае несанкционированного доступа они могут использоваться злоумышленниками. 

**Сведения о тесте**  
Проверяет наличие в пакете приложения файлов с расширениями PFX и SNK, которые указывают на наличие ключей подписывания частного кода. 

**Корректирующие действия**  
Удалите из пакета все ключи подписывания частного кода (например, файлы PFX и SNK).


## <a name="related-topics"></a>Связанные темы

* [Политики для Microsoft Store](https://docs.microsoft.com/legal/windows/agreements/store-policies)
