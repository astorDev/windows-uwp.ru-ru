---
layout: LandingPage
description: Эта страница содержит информацию о том, как приступить к разработке приложений UWP для платформ ARM64 и Win32.
title: Windows 10 на архитектуре ARM
author: msatranjr
ms.author: misatran
ms.date: 05/08/2018
ms.localizationpriority: medium
ms.topic: article
keywords: Windows 10 на ARM, ARM, разработка приложений Win32 и ARM64, разработка драйверов ARM64
ms.openlocfilehash: 83f2a0d03040a682e6965558174294fe27e21bfb
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57583360"
---
# <a name="windows-10-on-arm"></a>Windows 10 на архитектуре ARM
Windows 10 выполняется на компьютерах, использующих процессоры ARM. Эта страница содержит дополнительные сведения об этой платформе и о том, как приступить к разработке приложений для нее. Мы также рекомендуем вам оставить отзыв с помощью ссылок в нижней части страницы.

## <a name="introductory-videos"></a>Вводные видео
Смотрите видео, чтобы узнать, как Windows 10 выполняется на архитектуре ARM.

<ul class="cols cols3">
    <li>
        <a href="https://youtu.be/OZtVBDeVqCE"><img alt="Building ARM64 Win32 C++ apps video" src="./images/Arm64Scaled.png" /></a>
        <h3>Создание приложений Win32 для архитектуры ARM64 на C++</h3><p>Узнайте, как установить инструменты ARM64 для Visual Studio. Затем мы пошагово рассмотрим процессы создания и компиляции проекта ARM 64.</p>
    </li>
    <li>
        <a href="https://channel9.msdn.com/Events/Build/2018/BRK2438"><img alt="Build 2018 Windows 10 on ARM for developers" src="./images/buildVideoStillScaled.png" /></a>
        <h3>Build 2018: Windows 10 на архитектуре ARM для разработчиков</h3><p>Узнайте о том, как Windows 10 выполняется на устройствах ARM, как действует магия эмуляции x86 и, наконец, как отправлять и создавать приложения для Windows 10 на архитектуре ARM. Мы покажем способы создания классических приложений и приложений UWP для платформы ARM64.</p>
    </li>
    <li>
        <a href="https://channel9.msdn.com/Events/Ch9Live/Windows-Community-Standup/Kevin-Gallo-January-2018"><img alt="Community standup video featuring Kevin Gallo" src="./images/communityStandupStillScaled.png" /></a>
        <h3>Репортаж сообщества Windows с Кевином Галло</h3><p>Подробно изучите, как Windows 10 выполняется на архитектуре ARM64, и опробуйте приложения и возможности на этой платформе.</p>
    </li>
</ul>

## <a name="understanding-windows-10-on-arm"></a>Основные сведения о Windows 10 на архитектуре ARM
Изучите платформу, ознакомившись с этими ресурсами.

<ul class="cardsF panelContent cols cols2">
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <a class="card" href="/windows/uwp/porting/apps-on-arm" title="Приступая к работе" data-linktype="absolute-path">
                    <div class="cardImageOuter">
                            <img class="cardImage" role="presentation" alt="Get started icon" src="/media/common/i_get-started.svg" data-linktype="external" />
                    </div>
                </a>
                <div class="cardText">
                    <h3>Приступая к работе с Windows 10 на архитектуре ARM</h3>
                    <p class="x-hidden-focus">Ознакомьтесь с документацией, чтобы разобраться в основах.</p>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <a class="card" href="/windows/uwp/porting/apps-on-arm-x86-emulation" title="Раздел об эмуляции x86" data-linktype="absolute-path">
                    <div class="cardImageOuter">
                             <img class="cardImage" role="presentation" alt="x86 emulation icon" src="/media/common/i_advanced.svg" data-linktype="external" />
                    </div>
                </a>
                <div class="cardText">
                    <h3>Принципы работы эмуляции x86</h3>
                    <p class="x-hidden-focus">Изучите все об этой основной функции Windows 10 на архитектуре ARM.</p>
                </div>
            </div>
        </div>
    </li>
    <!--<li>
        <div class="cardSize">
            <div class="cardPadding">
                <a class="card" href="https://blogs.msdn.microsoft.com/harip/" data-linktype="absolute-path">
                    <div class="cardImageOuter">
                            <img class="cardImage" role="presentation" alt="" src="/media/common/i_blog.svg?branch=master" data-linktype="external" />
                            </a>
                    </div>
                </a>
                <div class="cardText">
                    <h3>Read the Kernel blog</h3>
                    <p class="x-hidden-focus">Get a deep understanding of the Windows by reading articles that are written by the creators of the kernel.</p>
                </div>
            </div>
        </div>
    </li>-->
</ul>

## <a name="developing-for-windows-10-on-arm"></a>Разработка для Windows 10 на архитектуре ARM
Начните адаптировать свои приложения для Windows 10 на архитектуре ARM и воспользуйтесь преимуществами функций этой платформы.  

<ul class="cardsF panelContent cols cols3">
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <a class="card" href="https://blogs.windows.com/buildingapps/?p=52087" title="Создание приложений ARM64" data-linktype="absolute-path">
                    <div class="cardImageOuter">
                            <img class="cardImage" role="presentation" alt="Build ARM64 Win32 apps blog icon" src="/media/common/i_build.svg" data-linktype="external" />
                    </div>
                    </a>
                <div class="cardText">
                    <h3>Создание приложений ARM64 с помощью пакета SDK</h3>
                    <p class="x-hidden-focus">Ознакомьтесь с этой записью блога, в которой пошагово рассматривается компиляция приложений ARM64 для запуска в Windows 10 на архитектуре ARM.</p>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <a class="card" href="/windows/uwp/porting/apps-on-arm-troubleshooting-arm32" title="Устранение неполадок в приложениях arm32" data-linktype="absolute-path">
                    <div class="cardImageOuter">
                            <img class="cardImage" role="presentation" alt="UWP apps on ARM icon" src="/media/common/i_code-edit.svg" data-linktype="external" />
                    </div>
                </a>
                <div class="cardText">
                    <h3>Приложения UWP для ARM</h3>
                    <p class="x-hidden-focus">Следуйте инструкциям этого руководства, чтобы настроить приложения универсальной платформы Windows (UWP) для успешной работы.</p>                    
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <a class="card" href="/windows-hardware/drivers/debugger/debugging-arm64" title="Отладка приложений ARM64" data-linktype="absolute-path">
                    <div class="cardImageOuter">
                             <img class="cardImage" role="presentation" alt="Debugging on ARM icon" src="/media/common/i_debug.svg" data-linktype="external" />
                    </div>
                </a>
                <div class="cardText">
                    <h3>Отладка на архитектуре ARM</h3>
                    <p class="x-hidden-focus">Обеспечьте беспроблемное выполнение своего кода в Windows 10 на архитектуре ARM.</p>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <a class="card" href="/windows-hardware/drivers/develop/building-arm64-drivers" title="Создание драйверов ARM64" data-linktype="absolute-path">
                    <div class="cardImageOuter">
                            <img class="cardImage" role="presentation" alt="Building ARM64 drivers icon" src="/media/common/i_drivers.svg" data-linktype="external" />
                            </a>
                    </div>
                </a>
                <div class="cardText">
                    <h3>Создание драйверов ARM64 с помощью WDK</h3>
                    <p class="x-hidden-focus">Перекомпилируйте драйверы для платформы ARM64.</p>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <a class="card" href="/windows/uwp/porting/apps-on-arm-troubleshooting-x86" title="Устранение неполадок в приложениях x86" data-linktype="absolute-path">
                    <div class="cardImageOuter">
                            <img class="cardImage" role="presentation" alt="x86 apps on ARM icon" src="/media/common/i_code-blocks.svg" data-linktype="external" />
                            </a>
                    </div>
                </a>
                <div class="cardText">
                    <h3>Приложения x86 для ARM</h3>
                    <p class="x-hidden-focus">Разрабатывайте приложения x86 для наиболее эффективной работы в Windows 10 на архитектуре ARM.</p>
                </div>
            </div>
        </div>
    </li>
</ul>

<!--## Other videos
<ul class="cols cols4">
<li>
        <a href="#"><img alt="" src="./images/dummyStillScaled.png" /></a>
            <p>TBD</p>    
    </li>
<li>
        <a href="#"><img alt="" src="./images/dummyStillScaled.png" /></a>
            <p>TBD</p>    
    </li>
<li>
        <a href="#"><img alt="" src="./images/dummyStillScaled.png" /></a>
            <p>TBD</p>    
    </li>
<li>
        <a href="#"><img alt="" src="./images/dummyStillScaled.png" /></a>
            <p>TBD</p>    
    </li>
</ul>-->

## <a name="let-us-know-if-you-have-feedback"></a>Отправьте нам свои отзывы
Мы постоянно улучшаем наш продукт с помощью отзывов, получаемых от вас и других клиентов. Если у вас возникла идея, вы не можете устранить проблему или просто хотите поделиться прекрасными впечатлениями от использования, то можете воспользоваться этими ссылками.

<ul class="cardsM cols cols3">
<li>
        <a class="card" href="feedback-hub://?tabid=2&contextid=803" data-linktype="absolute-path">
            <img class="cardImage" role="presentation" alt="Feedback hub icon" src="/media/common/i_feedback.svg" data-linktype="external" />
            <div class="cardText">
                <h3>Использование Центра отзывов</h3>
                <p>Мы что-то упустили? У вас есть отличная идея? Сообщите нам об этом в Центре отзывов.</p>
            </div>
        </a>
    </li>
    <li>
        <a class="card" href="mailto:woafeedback@microsoft.com" data-linktype="absolute-path">
            <img class="cardImage" role="presentation" alt="Report a bug icon" src="/media/common/i_mail.svg" data-linktype="external" />
            <div class="cardText">
                <h3>Сообщение об ошибке</h3>
                <p>Обнаружили ошибку в нашей платформе? Отправьте нам электронное сообщение с подробными сведениями.</p>
            </div>
        </a>
    </li>
    <li>
        <a class="card" href="https://github.com/MicrosoftDocs/windows-uwp/tree/docs/landing/arm-docs" data-linktype="absolute-path">
            <img class="cardImage" role="presentation" alt="Give doc feedback icon" src="/media/common/i_form.svg" data-linktype="external" />
            <div class="cardText">
                <h3>Отправка отзыва о документации</h3>
                <p>Обнаружили ошибку в нашей документации? Хотите, чтобы мы рассказали о чем-либо подробнее? Отправьте запрос в нашем репозитории GitHub, который называется docs.</p>
            </div>
        </a>
    </li>
</ul>