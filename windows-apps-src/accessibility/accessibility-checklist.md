---
Description: В статье представлен контрольный список, который поможет вам удостовериться, что приложение универсальной платформы Windows (UWP) поддерживает специальные возможности.
title: Контрольный список специальных возможностей
ms.assetid: BB8399E2-7013-4F77-AF2C-C1A0E5412856
label: Checklist
template: detail.hbs
---

Контрольный список специальных возможностей
===================================================================================

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В статье представлен контрольный список, который поможет вам удостовериться, что приложение универсальной платформы Windows (UWP) использует специальные возможности.

Здесь представлен контрольный список, при помощи которого вы можете удостовериться, что ваше приложение использует специальные возможности.

1.  Задайте доступное имя (обязательно) и описание (необязательно) для содержимого и интерактивных элементов пользовательского интерфейса своей программы.

    Доступное имя — это краткая описательная текстовая строка, используемая программой чтения с экрана для объявления элемента пользовательского интерфейса. Некоторые элементы пользовательского интерфейса, например [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/BR209652) и [**TextBox**](https://msdn.microsoft.com/library/windows/apps/BR209683), делают доступным именем по умолчанию свое текстовое содержимое (см. [Основные сведения о специальных возможностях](basic-accessibility-information.md#name_from_inner_text).

    Необходимо явно задать доступное имя для изображений и других элементов управления, которые не делают неявным специальным именем свое внутреннее текстовое содержимое. Следует использовать метки для элементов формы, чтобы текст метки можно было использовать в качестве целевого объекта [**LabeledBy**](https://msdn.microsoft.com/library/windows/apps/Hh759769) в модели автоматизации пользовательского интерфейса Майкрософт для сопоставления меток и вводимых данных. Если вы хотите предоставить пользователям больше сведений о пользовательском интерфейсе, чем обычно содержится в доступном имени, это можно сделать с помощью доступных описаний и подсказок.

    Дополнительные сведения см. в разделах [Доступное имя](basic-accessibility-information.md#accessible_name) и [Доступное описание](basic-accessibility-information.md).

2.  Реализуйте специальные возможности клавиатуры:


    -   Test the default tab index order for a UI. Adjust the tab index order if necessary, which may require enabling or disabling certain controls, or changing the default values of [**TabIndex**](https://msdn.microsoft.com/library/windows/apps/BR209461) on some of the UI elements.
    -   Use controls that support arrow-key navigation for composite elements. For default controls, the arrow-key navigation is typically already implemented.
    -   Use controls that support keyboard activation. For default controls, particularly those that support the UI Automation [**Invoke**](https://msdn.microsoft.com/library/windows/apps/BR242582) pattern, keyboard activation is typically available; check the documentation for that control.
    -   Set access keys or implement accelerator keys for specific parts of the UI that support interaction.
    -   For any custom controls that you use in your UI, verify that you have implemented these controls with correct [**AutomationPeer**](https://msdn.microsoft.com/library/windows/apps/BR209185) support for activation, and defined overrides for key handling as needed to support activation, traversal and access or accelerator keys.

    For more info, see [Keyboard interactions](https://msdn.microsoft.com/library/windows/apps/Mt185607).

3.  Визуально проверьте свой пользовательский интерфейс, чтобы удостовериться, что используется адекватный уровень контрастности текста, элементы в темах с высокой контрастностью отображаются правильно, а цвета используются надлежащим образом.

    -   Используя системные параметры отображения, регулирующие количество точек на дюйм, убедитесь в правильности масштабирования пользовательского интерфейса вашего приложения при изменении этого значения. (Некоторые пользователи используют значения числа точек на дюйм как параметр специальных возможностей, который доступен на панели **специальных возможностей**.)
    -   Используйте анализатор цветов, чтобы убедиться, что коэффициент контрастности текста составляет не менее 4,5:1.
    -   Выберите тему с высокой контрастностью и убедитесь, что пользовательский интерфейс вашей программы можно читать и использовать.
    -   Удостоверьтесь, что цвет не является единственным способом передачи информации в вашем пользовательском интерфейсе.

    Дополнительные сведения см. в статьях [Темы с высокой контрастностью](high-contrast-themes.md) и [Требования к специальным возможностям отображения текста](accessible-text-requirements.md).

4.  Запустите средства специальных возможностей, устраните выявленные проблемы и проверьте процесс чтения с экрана.

    Используйте такие инструменты как [**Inspect**](https://msdn.microsoft.com/library/windows/desktop/Dd318521) для проверки программного доступа; запустите средства диагностики, например [**AccChecker**](https://msdn.microsoft.com/library/windows/desktop/Hh920985), чтобы обнаружить общие ошибки; проверьте также процесс чтения с экрана с помощью экранного диктора.

    Дополнительную информацию см. в статье [Тестирование специальных возможностей](accessibility-testing.md).

5.  Убедитесь, что параметры манифеста вашего приложения соответствуют рекомендациям по реализации специальных возможностей.

6.  Объявите о специальных возможностях своей программы в Магазине Windows.

    Если в приложении реализована поддержка основных специальных возможностей и вы объявили об этом в Магазине Windows, вы сможете привлечь больше клиентов и получить больше хороших оценок.

    Дополнительную информацию см. в статье [Специальные возможности в Магазине](accessibility-in-the-store.md).

<span id="related_topics">
            </span>Связанные разделы
-----------------------------------------------

* [Специальные возможности](accessibility.md)
* [Рекомендации по специальным возможностям](https://msdn.microsoft.com/library/windows/apps/Hh700407)
* [Нерекомендуемые методики](practices-to-avoid.md)
 

 





<!--HONumber=Mar16_HO3-->


