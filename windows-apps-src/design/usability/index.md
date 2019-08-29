---
description: Узнайте, как сделать ваше приложение доступным для людей во всем мире.
keywords: специальные возможности приложений uwp, глобализация, приложения с конструированием, требования к приложению со специальными возможностями
title: Удобство использования в приложениях UWP — разработка приложений для Windows
layout: LandingPage
template: detail.hbs
ms.date: 10/18/2017
ms.topic: article
ms.assetid: e6bb3464-dd8e-402c-9c56-dd9e51002a49
ms.localizationpriority: medium
ms.openlocfilehash: a6912932b7ad71fd3d04c038eab7e0aa4dd6cb11
ms.sourcegitcommit: 2fa2d2236870eaabc95941a95fd4e358d3668c0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076392"
---
# <a name="usability-for-uwp-apps"></a>Удобство использования приложений UWP

Чтобы сделать из хорошего приложения превосходный продукт, который оценят пользователи во всем мире, требуются небольшие штрихи и особое внимание к деталям.

Инструкции по оформлению и написанию кода в этом разделе помогут вам расширить целевую аудиторию вашего приложения UWP за счет специальных возможностей, глобализации и локализации, поддержки настройки приложений пользователями и предоставления справки, когда она необходима.

## <a name="accessibility"></a>Специальные возможности

Специальные возможности делают приложение доступным для тех людей, которые из-за проблем со здоровьем не могут пользоваться стандартным пользовательским интерфейсом. В некоторых ситуациях специальные возможности требуются законодательством. Однако рекомендуется реализовывать специальные возможности даже там, где они не обязательны по закону, так как это расширит аудиторию вашего приложения.

[Портал специальных возможностей](../accessibility/accessibility.md)

<!--
<ul class="panelContent cardsH" style="margin-left: 1px">
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../accessibility/accessibility-overview.md">Accessibility overview</a></b> <br/> This article is an overview of the concepts and technologies related to accessibility scenarios for UWP apps.</p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../accessibility/designing-inclusive-software.md">Designing inclusive software</a></b><br/>Learn about evolving inclusive design with Universal Windows Platform (UWP) apps for Windows 10.  Design and build inclusive software with accessibility in mind.</p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../accessibility/developing-inclusive-windows-apps.md">Developing inclusive Windows apps</a></b><br/> This article is a roadmap for developing accessible UWP apps.</p>
                    </div>
                </div>
            </div>
        </div>
    </li> 
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../accessibility/accessibility-testing.md">Accessibility testing</a> </b><br/>Testing procedures to follow to ensure that your UWP app is accessible.</p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../accessibility/accessibility-in-the-store.md">Accessibility in the Store</a></b><br/>Describes the requirements for declaring your UWP app as accessible in the Microsoft Store.</p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../accessibility/accessibility-checklist.md">Accessibility checklist</a></b><br/>Provides a checklist to help you ensure that your UWP app is accessible.</p>
                    </div>
                </div>
            </div>
        </div>
    </li>        
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../accessibility/basic-accessibility-information.md">Expose basic accessibility information</a></b><br/>Basic accessibility info is often categorized into name, role, and value. This topic describes code to help your app expose the basic information that assistive technologies need.</p>
                    </div>
                </div>
            </div>
        </div>
    </li> 
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../accessibility/keyboard-accessibility.md">Keyboard accessibility</a></b><br/>If your app does not provide good keyboard access, users who are blind or have mobility issues can have difficulty using your app or may not be able to use it at all.</p>
                    </div>
                </div>
            </div>
        </div>
    </li> 
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../accessibility/high-contrast-themes.md">High-contrast themes</a></b><br/>Describes the steps needed to ensure your UWP app is usable when a high-contrast theme is active. </p>
                    </div>
                </div>
            </div>
        </div>
    </li>         
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../accessibility/accessible-text-requirements.md">Accessible text requirements</a></b><br/>This topic describes best practices for accessibility of text in an app, by assuring that colors and backgrounds satisfy the necessary contrast ratio. This topic also discusses the Microsoft UI Automation roles that text elements in a UWP app can have, and best practices for text in graphics.</p>                    
                    </div>
                </div>
            </div>
        </div>
    </li>     
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../accessibility/practices-to-avoid.md">Accessibility practices to avoid</a></b><br/>Lists the practices to avoid if you want to create an accessible UWP app.</p>                    
                    </div>
                </div>
            </div>
        </div>
    </li>     
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../accessibility/custom-automation-peers.md">Custom automation peers</a></b><br/>Describes the concept of automation peers for UI Automation, and how you can provide automation support for your own custom UI class.</p>                    
                    </div>
                </div>
            </div>
        </div>
    </li>     
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../accessibility/control-patterns-and-interfaces.md">Control patterns and interfaces</a></b><br/>Lists the Microsoft UI Automation control patterns, the classes that clients use to access them, and the interfaces providers use to implement them.</p>                    
                    </div>
                </div>
            </div>
        </div>
    </li>     
</ul>
-->

## <a name="app-settings"></a>Параметры приложения

Параметры позволяют пользователям настраивать приложение, оптимизируя его в соответствии с индивидуальными потребностями и предпочтениями. Предоставление подходящих параметров и их хранение сделают отличный процесс взаимодействия с пользователем еще лучше.

<ul class="panelContent cardsH" style="margin-left: 1px">
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../app-settings/guidelines-for-app-settings.md">Руководство</a></b><br/>Рекомендации по созданию и отображению параметров приложения.</p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../app-settings/store-and-retrieve-app-data.md">Сохранение и получение данных приложения</a></b><br/>Хранение и извлечение локальных, перемещаемых и временных данных приложений.</p>
                    </div>
                </div>
            </div>
        </div>
    </li>
</ul>

## <a name="globalization-and-localization"></a>Глобализация и локализация

Windows используется во всем мире пользователями с разной культурой, живущими в разных регионах и говорящими на разных языках. Пользователи говорят на множестве различных языков, в различных странах и регионах. Некоторые пользователи говорят на нескольких языках. Таким образом, ваше приложение используется в конфигурациях, которые включают в себя много вариантов системных параметров для языка и региона. Расширьте потенциальный рынок для вашего приложения, используя *глобализацию* и *локализацию* для быстрой адаптации к любому региону.

<a href="../globalizing/globalizing-portal.md">Портал глобализации и локализации</a>

## <a name="in-app-help"></a>Справка в приложении
Как бы хорошо вы ни спроектировали приложение, некоторым пользователям может понадобиться дополнительная помощь.

<ul class="panelContent cardsH" style="margin-left: 1px">
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../in-app-help/guidelines-for-app-help.md">Руководство по справке для приложений</a></b><br/>Приложения могут быть сложными, поэтому предоставление пользователям эффективной справки может существенно улучшить работу с приложением.
</p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../in-app-help/instructional-ui.md">Инструктивный пользовательский интерфейс</a></b><br/>Иногда бывает полезно обучить пользователя работе с неочевидными функциями вашего приложения, например с сенсорными методами взаимодействия. В этих случаях необходимо предоставить пользователю инструкции в пользовательском интерфейсе, чтобы он мог найти и воспользоваться функциями, о наличии которых он, возможно, и не подозревал.</p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../in-app-help/in-app-help.md">Справка в приложении</a></b><br/>В большинстве случаев оптимальным является отображение справки внутри приложения тогда, когда пользователь сам решит к ней обратиться. Рассмотрите следующие рекомендации при создании справки в приложении.</p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
<p><b><a href="../in-app-help/external-help.md">Внешняя справка</a></b><br/>В большинстве случаев оптимальным является отображение справки внутри приложения тогда, когда пользователь сам решит к ней обратиться. Рассмотрите следующие рекомендации при создании справки в приложении.</p>
                    </div>
                </div>
            </div>
        </div>
    </li>        
</ul>

