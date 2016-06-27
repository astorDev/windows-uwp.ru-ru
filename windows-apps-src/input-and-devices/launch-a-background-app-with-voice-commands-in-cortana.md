---
author: Karl-Bridge-Microsoft
Description: "Помимо использования голосовых команд Кортаны для получения доступа к системным компонентам, вы также можете расширить возможности Кортаны за счет компонентов и функций из фонового приложения с помощью голосовых команд, которые определяют действие или команду для выполнения в приложении."
title: "Запуск фонового приложения с помощью голосовых команд в Кортане"
ms.assetid: DF5B530C-57DD-4CA5-B3BE-1A0B3695C9C6
label: Launch a background app
template: detail.hbs
ms.sourcegitcommit: 7d9f5eff0f6561b18024658fe99d1e11bbe3309f
ms.openlocfilehash: c65abdda905a390567d3c2b199a891c0c3067df1

---

# Активация фонового приложения с помощью голосовых команд в Кортане

Помимо использования голосовых команд **Кортаны** для получения доступа к системным компонентам, вы также можете расширить возможности **Кортаны** за счет компонентов и функций вашего приложения (в качестве фоновой задачи) с помощью голосовых команд, которые определяют действие или команду для выполнения в приложении. Если приложение обрабатывает голосовую команду в фоновом режиме, она не принимает на себя фокус. Она возвращает всю обратную связь и результаты на холсте **Кортаны** и с помощью голосовых функций **Кортаны**.

**Важные API**

-   [**Windows.ApplicationModel.VoiceCommands**](https://msdn.microsoft.com/library/windows/apps/dn706594)
-   [**Элементы и атрибуты в VCD-файле, версия 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593)



Приложения можно активировать на переднем плане (тогда приложение оказывается в фокусе) или в фоновом режиме (в этом случае **Кортана** сохраняет фокус) в зависимости от сложности взаимодействия. Например, голосовые команды, для которых требуется дополнительный контекст или пользовательский ввод (такой как отправка сообщения определенному контакту), наилучшим образом обрабатываются на переднем плане, а основные команды (перечисление предстоящих поездок) при этом могут обрабатываться в **Кортане** в фоновом приложении.

Если требуется активировать приложение на переднем плане с помощью голосовых команд, см. инструкции в разделе [Активация приложения переднего плана с помощью голосовых команд Кортаны](launch-a-foreground-app-with-voice-commands-in-cortana.md).

> **Примечание.**  
> Голосовая команда — это одно изречение, имеющее конкретную цель, определенное в файле определения голосовых команд (VCD) и направленное на установленное приложение через **Кортану**.

> VCD-файл определяет одну или несколько голосовых команд, каждая из которых несет в себе уникальное намерение.

> Определение голосовых команд может иметь разную сложность. Определение может поддерживать от одного ограниченного изречения до набора более гибких, свойственных языку, изречений, которые имеют один и тот же смысл.

Чтобы продемонстрировать функции фонового приложения, воспользуемся приложением для планирования поездок и управления ими под названием **Adventure Works** из [примера голосовой команды Кортаны](http://go.microsoft.com/fwlink/p/?LinkID=619899).

Вот обзор приложения **Adventure Works**, интегрированного с холстом **Кортаны**.

![Обзор холста Кортаны ](images/cortana-overview.png)

Чтобы просмотреть поездку **Adventure Works** без **Кортаны**, пользователь запустил бы приложение и перешел на страницу **Предстоящие поездки**.

Используя голосовые команды в **Кортане** для запуска приложения в фоновом режиме, пользователь может просто произнести: «Adventure Works, когда я еду в Лас-Вегас?» Ваше приложение обрабатывает команду, и **Кортана** отображает результаты вместе со значком приложения и другими сведениями о приложении, если они предоставлены. Вот пример базового запроса поездки и экрана результатов **Кортаны**, который отображается и произносит: «Ваша следующая поездка в Лас-Вегас — в пятницу 31 июля 2015 года».

![Базовый запрос и экран результатов, который использует приложение Adventure Works в фоновом режиме](images/cortana-backgroundapp-result.png)

Далее перечислены основные шаги по добавлению функций голосовых команд и расширению **Кортаны** за счет фоновых функций приложения при помощи речевого ввода или ввода с клавиатуры.

1.  Создайте службу приложения (см. раздел [**Windows.ApplicationModel.AppService**](https://msdn.microsoft.com/library/windows/apps/dn921731)), которую **Кортана** вызывает в фоновом режиме.
2.  Создайте VCD-файл. Это XML-документ, определяющий все фразы, произносимые пользователем для выполнения действий или команд при активации приложения. См. раздел [**Элементы и атрибуты VCD вер. 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593).
3.  Зарегистрируйте наборы команд в VCD-файле при запуске приложения.
4.  Осуществите фоновую активацию службы приложения и выполнение голосовой команды.
5.  Отобразите и произнесите соответствующий отзыв о голосовой команде в **Кортане**.

**Предварительные требования:  **

Если вы — начинающий разработчик приложений универсальной платформы для Windows (UWP), прочитайте указанные ниже статьи, чтобы ознакомиться с описанными здесь технологиями.

-   [Создание первого приложения](https://msdn.microsoft.com/library/windows/apps/bg124288)
-   Дополнительную информацию о событиях см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/library/windows/apps/mt185584).

**Рекомендации по взаимодействию с пользователем.  **

Сведения об интеграции приложения с **Кортаной** см. в [рекомендациях по проектированию Кортаны](https://msdn.microsoft.com/library/windows/apps/dn974233), а полезные советы по проектированию удобного и привлекательного приложения с поддержкой распознавания речи — в [рекомендациях по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121).

## <span id="Create_a_new_solution_with_a_primary_project_in_Visual_Studio"></span><span id="create_a_new_solution_with_a_primary_project_in_visual_studio"></span><span id="CREATE_A_NEW_SOLUTION_WITH_A_PRIMARY_PROJECT_IN_VISUAL_STUDIO"></span>Создание решения с основным проектом в Visual Studio


1.  Запустите Microsoft Visual Studio 2015.

    Откроется начальный экран Visual Studio 2015.

2.  В меню **Файл** выберите элемент **Создать** > **Проект**.

    Откроется диалоговое окно **Создание проекта**. Левая панель диалогового окна позволяет выбрать тип отображаемого шаблона.

3.  На левой панели разверните узел **Установленные > Шаблоны > Visual C\# > Windows** и выберите группу шаблонов **Универсальные**. На центральной панели диалогового окна отображается список шаблонов проектов для приложений универсальной платформы Windows (UWP).
4.  На центральной панели выберите шаблон **Пустое приложение (универсальное приложение для Windows)**.

    Шаблон **Пустое приложение** дает возможность создать обладающее минимальным функционалом приложение UWP, которое компилируется и запускается, но не содержит данных или элементов управления пользовательского интерфейса. Вы будете добавлять элементы управления и данные в процессе работы с этим руководством.

5.  В текстовом поле **Имя** введите имя вашего проекта. В этом примере мы используем имя "AdventureWorks".
6.  Чтобы создать проект, нажмите кнопку **ОК**.

    Microsoft Visual Studio создаст проект, который появится в **обозревателе решений**.


## <span id="Add_image_assets_to_primary_project_and_specify_them_in_the_app_manifest"></span><span id="add_image_assets_to_primary_project_and_specify_them_in_the_app_manifest"></span><span id="ADD_IMAGE_ASSETS_TO_PRIMARY_PROJECT_AND_SPECIFY_THEM_IN_THE_APP_MANIFEST"></span>Добавление ресурсов изображений в проект и указание их в манифесте приложения
      
Приложения UWP могут автоматически выбирать наиболее подходящие изображения на основе определенных параметров и возможностей устройства (высокая контрастность, эффективные пиксели, язык и т. д.). Достаточно предоставить изображения, придерживаясь соответствующего соглашения об именовании и используя правильную структуру папок в проекте приложения для различных версий ресурсов. Если не предоставить рекомендуемые версии ресурсов, это может отрицательно сказаться на специальных возможностях, качестве локализации и изображений в зависимости от пользовательских настроек, возможностей, типа устройства и местоположения.

Дополнительную информацию о ресурсах изображений для высокой контрастности и коэффициентов масштабирования см. в статье [Руководство по работе с ресурсами плиток и значков](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets).

Назовите ресурсы с помощью квалификаторов. Квалификаторы ресурсов — это модификаторы папки и имени файла, которые определяют контекст использования версии ресурса.

Стандартное соглашение об именовании — это `foldername/qualifiername-value[_qualifiername-value]/filename.qualifiername-value[_qualifiername-value].ext`. Например, объект `images/en-US/logo.scale-100_contrast-white.png`, на который легко сослаться в коде, указав корневую папку и имя файла: `images/logo.png`. См. раздел [Как присваивать имена ресурсам с помощью квалификаторов](https://msdn.microsoft.com/library/windows/apps/xaml/hh965324.aspx).

Рекомендуется пометить язык по умолчанию и строковые файлы ресурсов (например, `en-US\resources.resw`), а также стандартный коэффициент масштабирования изображений (например, `logo.scale-100.png`), даже если вы не планируете в настоящий момент предоставлять локализованные ресурсы или ресурсы с разными разрешениями. Тем не менее, мы настоятельно рекомендуем предоставлять ресурсы с коэффициентами масштабирования 100, 200 и 400.

> *Важно!

> Значок приложения, используемый в области названия холста **Кортаны** — это значок Square44x44Logo, определенный в файле "Package.appxmanifest". 

> Также можно указать значок для каждой записи в области содержимого на холсте **Кортаны**. Допустимые размеры изображения для значков результатов:

> - 68×68 (Ш×В) 
> - 68×92 (Ш×В) 
> - 280×140 (Ш×В) 

Плитка содержимого не подтверждается до тех пор, пока объект [**VoiceCommandResponse**](https://msdn.microsoft.com/library/windows/apps/dn974182) не будет передан объекту [**VoiceCommandServiceConnection**](https://msdn.microsoft.com/library/windows/apps/dn974204). Если передать объект **VoiceCommandResponse** **Кортане**, которая включает плитку содержимого с изображением, не соответствующим этим размерам, может возникнуть исключение. 

В этом примере из приложения **Adventure Works** (VoiceCommandService\\AdventureWorksVoiceCommandService.cs), мы задаем простой серый квадрат (GreyTile.png) на плитке [**VoiceCommandContentTile**](https://msdn.microsoft.com/library/windows/apps/dn974168) с использованием шаблона плитки [**TitleWith68x68IconAndText**](https://msdn.microsoft.com/library/windows/apps/dn974169). Варианты логотипа находятся в папке VoiceCommandService\\Images и извлекаются с помощью метода [**GetFileFromApplicationUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701741).

```CSharp
var destinationTile = new VoiceCommandContentTile();

destinationTile.ContentTileType = 
  VoiceCommandContentTileType.TitleWith68x68IconAndText;
destinationTile.Image = 
  await StorageFile.GetFileFromApplicationUriAsync(
    new Uri("ms-appx:///AdventureWorks.VoiceCommands/Images/GreyTile.png"));
```

## <span id="Create_an_app_service_project"></span><span id="create_an_app_service_project"></span><span id="CREATE_AN_APP_SERVICE_PROJECT"></span>Создание проекта службы приложения

<ol>
    <li>
Щелкните правой кнопкой мыши название своего решения и выберите **Создать > Проект**.
    </li>
    <li>
В разделе **Установленные > Шаблоны > Visual C# > Windows > Универсальные** выберите **Windows Runtime Component**. Это компонент, который реализует службу приложения (см. раздел **[Windows.ApplicationModel.AppService](https://msdn.microsoft.com/library/windows/apps/dn921731)**).
    </li>
    <li>
Введите имя проекта (например, VoiceCommandService) и нажмите кнопку **ОК**.
    </li>
    <li>
В **обозревателе решений** выберите проект VoiceCommandService и переименуйте файл Class1.cs, созданный в Visual Studio. В примере с **Adventure Works** используется файл AdventureWorksVoiceCommandService.cs.
    </li>
    <li>
Нажмите кнопку **Д** в ответ на запрос о переименовании всех вхождений Class1.cs. 
    </li>
    <li>
В файле AdventureWorksVoiceCommandService.cs выполните следующие действия: <ol type="i">
 <li>
Добавьте следующее, воспользовавшись директивой.  
 ```using Windows.ApplicationModel.Background;```
 </li>
 <li>
При создании нового проекта имя проекта используется как корневое пространство имен по умолчанию во всех файлах. Переименуйте пространство имен, чтобы вложить в него код службы приложения в составе основного проекта. Например, `namespace AdventureWorks.VoiceCommands`. 
 </li>
 <li>
Правой кнопкой мыши щелкните имя проекта службы приложения в обозревателе решений и выберите **Свойства**. 
 </li>
 <li>
На вкладке **Библиотека** обновите значение в поле **Пространство имен по умолчанию**, указав то же значение (например, AdventureWorks.VoiceCommands). 
 </li>
 <li>
Создайте новый класс, реализующий интерфейс [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794). Этот класс требует метода [**Run**](https://msdn.microsoft.com/library/windows/apps/br224811), который является точкой входа, когда Кортана распознает голосовую команду. 
 </li>
        </ol>
    </li>
</ol>

Это базовый класс фоновых задач из приложения **Adventure Works**. Позже мы предоставим дополнительную информацию по этому вопросу.
> **Примечание.**    
> Класс фоновой задачи и все остальные классы в фоновой задаче должны быть запечатанными открытыми классами.
 
``` csharp
namespace AdventureWorks.VoiceCommands
{
    ...
    
    /// <summary>
    /// The VoiceCommandService implements the entry point for all voice commands.
    /// The individual commands supported are described in the VCD xml file. 
    /// The service entry point is defined in the appxmanifest.
    /// </summary>
    public sealed class AdventureWorksVoiceCommandService : IBackgroundTask
    {
        ...
        
        /// <summary>
        /// The background task entrypoint. 
        /// 
        /// Background tasks must respond to activation by Cortana within 0.5 seconds, and must 
        /// report progress to Cortana every 5 seconds (unless Cortana is waiting for user
        /// input). There is no execution time limit on the background task managed by Cortana,
        /// but developers should use plmdebug (https://msdn.microsoft.com/library/windows/hardware/jj680085%28v=vs.85%29.aspx)
        /// on the Cortana app package in order to prevent Cortana timing out the task during
        /// debugging.
        /// 
        /// The Cortana UI is dismissed if Cortana loses focus. 
        /// The background task is also dismissed even if being debugged. 
        /// Use of Remote Debugging is recommended in order to debug background task behaviors. 
        /// Open the project properties for the app package (not the background task project), 
        /// and enable Debug -> "Do not launch, but debug my code when it starts". 
        /// Alternatively, add a long initial progress screen, and attach to the background task process while it executes.
        /// </summary>
        /// <param name="taskInstance">Connection to the hosting background service process.</param>
        public void Run(IBackgroundTaskInstance taskInstance)
        {
        
          //
          // TODO: Insert code 
          //
          //
        
    }        
  }
}
```

<ol start="7">
    <li>
Объявите фоновую задачу как **AppService** в манифесте приложения.
    <ol type="i">
        <li>
В **обозревателе решений** щелкните правой кнопкой мыши файл Package.appxmanifest и выберите команду **Перейти к коду**. 
        </li>
        <li>
Найдите элемент [**Application**](https://msdn.microsoft.com/library/windows/apps/dn934738).
        </li>
        <li>
Добавьте элемент [**Extensions**](https://msdn.microsoft.com/library/windows/apps/dn934720) к элементу [**Application**](https://msdn.microsoft.com/library/windows/apps/dn934738).
        </li>
        <li>
Добавьте элемент [**uap:Extension**](https://msdn.microsoft.com/library/windows/apps/dn986788) к элементу [**Extensions**](https://msdn.microsoft.com/library/windows/apps/dn934720).
        </li>
        <li>Добавьте атрибут **Category** в элемент **uap:Extension** и установите для атрибута **Category** значение windows.appService.
        </li>
        <li>
Добавьте атрибут **EntryPoint** в элемент **uap:Extension** и установите в качестве значения для атрибута **EntryPoint** имя класса, который реализует [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794), в данном случае — AdventureWorks.VoiceCommands.AdventureWorksVoiceCommandService.
        </li>
        <li>
Добавьте элемент [**uap:AppService**](https://msdn.microsoft.com/library/windows/apps/dn934779) к элементу **uap:Extension**.
        </li>
        <li>
Добавьте атрибут **Name** в элемент [**uap:AppService**](https://msdn.microsoft.com/library/windows/apps/dn934779) и установите в качестве значения атрибута **Name** имя службы приложений, в данном случае — AdventureWorksVoiceCommandService.
        </li>
        <li>
Добавьте второй элемент [**uap:Extension**](https://msdn.microsoft.com/library/windows/apps/dn986788) к элементу [**Extensions**](https://msdn.microsoft.com/library/windows/apps/dn934720).
        </li>
        <li>
Добавьте атрибут **Category** к элементу [**uap:Extension**](https://msdn.microsoft.com/library/windows/apps/dn986788) и задайте для атрибута **Category** значение windows.personalAssistantLaunch.
        </li>
    </li> 
    </ol>
    </li>    
</ol>  

Ниже представлен манифест из приложения Adventure Works:
```xml
<Package>
  <Applications>
    <Application>

      <Extensions>
        <uap:Extension Category="windows.appService" 
          EntryPoint="CortanaBack1.VoiceCommands.CortanaBack1VoiceCommandService">
          <uap:AppService Name="CortanaBack1VoiceCommandService"/>
        </uap:Extension>
        <uap:Extension Category="windows.personalAssistantLaunch"/>
      </Extensions>

    <Application>
  <Applications>
</Package>
```

<ol start="8">
    <li>
Добавьте этот проект службы приложения в основной проект в качестве ссылки. 
    <ol type="i">
        <li>
Щелкните **Ссылки** правой кнопкой мыши. 
        </li>
        <li>
Выберите **Добавить ссылку...** 
        </li>
        <li>
В диалоговом окне **Диспетчер ссылок** разверните раздел **Проекты** и выберите проект службы приложения. 
        </li>
        <li>
Нажмите кнопку "ОК". 
        </li>
    </ol>
    </li>
</ol>

## <span id="Create_a_VCD_file"></span><span id="create_a_vcd_file"></span><span id="CREATE_A_VCD_FILE"></span>Создание VCD-файла


1. В Visual Studio щелкните правой кнопкой мыши имя основного проекта и выберите элементы **Добавить > Новый элемент**. Добавьте **XML-файл**.
2. Введите имя [**VCD-файла**](https://msdn.microsoft.com/library/windows/apps/dn706593) (например, AdventureWorksCommands.xml) и нажмите кнопку "Добавить". 
3. В **обозревателе решений** выберите файл [**VCD**](https://msdn.microsoft.com/library/windows/apps/dn706593).
4.  В окне **Свойства** задайте для элемента **Действие при сборке** значение **Содержимое**, а для элемента **Копировать в выходной каталог** — значение **Копировать более позднюю версию**.

## <span id="Edit_the_VCD_file"></span><span id="edit_the_vcd_file"></span><span id="EDIT_THE_VCD_FILE"></span>Редактирование VCD-файла

1. Добавьте элемент **VoiceCommands** с атрибутом **xmlns**, указывающим на `http://schemas.microsoft.com/voicecommands/1.2`.

2. Для каждого языка, поддерживаемого приложением, создайте элемент [**CommandSet**](https://msdn.microsoft.com/library/windows/apps/dn722331), который содержит голосовые команды, поддерживаемые приложением.

  Можно объявить несколько элементов [**CommandSet**](https://msdn.microsoft.com/library/windows/apps/dn722331) с разными атрибутами [**xml:lang**](https://msdn.microsoft.com/library/windows/apps/dn722331), чтобы приложение было пригодно к использованию на различных рынках. Например, приложение для США может иметь элемент [**CommandSet**](https://msdn.microsoft.com/library/windows/apps/dn722331) для английского языка и элемент [**CommandSet**](https://msdn.microsoft.com/library/windows/apps/dn722331) для испанского.

  >  **Внимание!**  
  Для активации приложения и выполнения действия с помощью голосовой команды приложение должно зарегистрировать VCD-файл, содержащий элемент [**CommandSet**](https://msdn.microsoft.com/library/windows/apps/dn722331) с языком, который совпадает с выбранным пользователем языком голосовых функций на устройстве. Язык голосовых функций находится в папке **Параметры > Система > Голосовые функции > Язык голосовых функций**.

3. Добавьте элемент **Command** для каждой команды, поддержку которой необходимо обеспечить.

  Каждая команда (**Command**), объявленная в файле [**VCD**](https://msdn.microsoft.com/library/windows/apps/dn706593), должна содержать приведенную ниже информацию.

  - Атрибут **Name**, используемый вашим приложением для идентификации голосовой команды в среде выполнения. 
  - Элемент **Example**, который содержит фразу, описывающую возможные способы вызова команды пользователем. **Кортана** показывает этот пример, когда пользователь говорит "Что можно говорить?", "Помоги" или выбирает пункт **Подробнее**.    
  -   Элемент **ListenFor**, содержащий слова или фразы, которые приложение распознает в качестве команды. Каждый элемент **ListenFor** может содержать ссылки на один или несколько элементов **PhraseList** с конкретными словами, связанными с этой командой.
  > **Примечание.**  
Элементы  **ListenFor** невозможно изменить программно. Однако программно можно изменять элементы **PhraseList**, связанные с элементами **ListenFor**. Приложения должны изменять содержимое **PhraseList** во время выполнения на основе созданного набора данных, когда пользователь использует приложение. Ознакомьтесь со статьей [Динамическое изменение списков фраз определения голосовых команд (VCD)](dynamically-modify-voice-command-definition--vcd--phrase-lists.md).

  -   Элемент **Feedback**, содержащий текст, который **Кортана** отображает и произносит при запуске приложения.

Элемент **Navigate** с атрибутом, который указывает, что по голосовой команде приложение будет запущено на переднем плане. В этом примере команда ```showTripToDestination``` — это задача переднего плана.

Элемент **VoiceCommandService** указывает, что голосовая команда запускает приложение в фоновом режиме. Значение атрибута **Target** этого элемента должно соответствовать значению атрибута **Name** элемента [**uap:AppService**](https://msdn.microsoft.com/library/windows/apps/dn934779) в файле package.appxmanifest. В этом примере команды ```whenIsTripToDestination``` и ```cancelTripToDestination``` — это фоновые задачи, указывающие имя службы приложения в следующем виде:AdventureWorksVoiceCommandService.

Дополнительные сведения см. в статье [**Элементы и атрибуты VCD вер. 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593).

Ниже приводится фрагмент файла [**VCD**](https://msdn.microsoft.com/library/windows/apps/dn706593), определяющий голосовые команды на американском английском (en-us) для приложения **Adventure Works**.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<VoiceCommands xmlns="http://schemas.microsoft.com/voicecommands/1.2">
  <CommandSet xml:lang="en-us" Name="AdventureWorksCommandSet_en-us">
    <AppName> Adventure Works </AppName>
    <Example> Show trip to London </Example>

    <Command Name="showTripToDestination">
      <Example> Show trip to London </Example>
      <ListenFor RequireAppName="BeforeOrAfterPhrase"> show [my] trip to {destination} </ListenFor>
      <ListenFor RequireAppName="ExplicitlySpecified"> show [my] {builtin:AppName} trip to {destination} </ListenFor>
      <Feedback> Showing trip to {destination} </Feedback>
      <Navigate />
    </Command>

    <Command Name="whenIsTripToDestination">
      <Example> When is my trip to Las Vegas?</Example>
      <ListenFor RequireAppName="BeforeOrAfterPhrase"> when is [my] trip to {destination}</ListenFor>
      <ListenFor RequireAppName="ExplicitlySpecified"> when is [my] {builtin:AppName} trip to {destination} </ListenFor>
      <Feedback> Looking for trip to {destination}</Feedback>
      <VoiceCommandService Target="AdventureWorksVoiceCommandService"/>
    </Command>
    
    <Command Name="cancelTripToDestination">
      <Example> Cancel my trip to Las Vegas </Example>
      <ListenFor RequireAppName="BeforeOrAfterPhrase"> cancel [my] trip to {destination}</ListenFor>
      <ListenFor RequireAppName="ExplicitlySpecified"> cancel [my] {builtin:AppName} trip to {destination} </ListenFor>
      <Feedback> Cancelling trip to {destination}</Feedback>
      <VoiceCommandService Target="AdventureWorksVoiceCommandService"/>
    </Command>

    <PhraseList Label="destination">
      <Item>London</Item>
      <Item>Las Vegas</Item>
      <Item>Melbourne</Item>
      <Item>Yosemite National Park</Item>
    </PhraseList>
  </CommandSet>
```

## <span id="Install_the_VCD_commands"></span><span id="install_the_vcd_commands"></span><span id="INSTALL_THE_VCD_COMMANDS"></span>Установка команд VCD

Для установки файла VCD приложение нужно запустить хотя бы один раз. 

>  **Примечание.**  
Данные о голосовых командах не сохраняются в ходе установки приложения. Чтобы обеспечить сохранность данных о голосовых командах для вашего приложения, инициализируйте файл VCD всякий раз при запуске или активации приложения или сохраняйте параметр, указывающий, установлен ли файл VCD в настоящее время.

В файле app.xaml.cs:

1. Добавьте следующее, воспользовавшись директивой:  
```csharp
using Windows.Storage;
```
2. Пометьте метод OnLaunched асинхронным модификатором.  
```csharp
protected async override void OnLaunched(LaunchActivatedEventArgs e)
```
3. Вызовите метод [**InstallCommandDefinitionsFromStorageFileAsync**](https://msdn.microsoft.com/library/windows/apps/dn708205) в обработчике [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335), чтобы зарегистрировать голосовые команды, которые должны распознаваться системой.

  В примере с приложением Adventure Works мы сначала определяем объект [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171). 

  Затем вызываем [**GetFileAsync**](https://msdn.microsoft.com/library/windows/apps/br227272), чтобы инициализировать его с помощью файла AdventureWorksCommands.xml.

  Далее этот объект [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171) передается в [**InstallCommandDefinitionsFromStorageFileAsync**](https://msdn.microsoft.com/library/windows/apps/dn708205).    
```CSharp
try
{
  // Install the main VCD. 
  StorageFile vcdStorageFile = 
    await Package.Current.InstalledLocation.GetFileAsync(
      @"AdventureWorksCommands.xml");

  await Windows.ApplicationModel.VoiceCommands.VoiceCommandDefinitionManager.
    InstallCommandDefinitionsFromStorageFileAsync(vcdStorageFile);

  // Update phrase list.
  ViewModel.ViewModelLocator locator = App.Current.Resources["ViewModelLocator"] as ViewModel.ViewModelLocator;
  if(locator != null)
  {
     await locator.TripViewModel.UpdateDestinationPhraseList();
  }
}
catch (Exception ex)
{
  System.Diagnostics.Debug.WriteLine("Installing Voice Commands Failed: " + ex.ToString());
}
```

## <span id="Handle_activation_and_execute_voice_commands"></span><span id="handle_activation_and_execute_voice_commands"></span><span id="HANDLE_ACTIVATION_AND_EXECUTE_VOICE_COMMANDS"></span>Обработка активации

Укажите, как приложение должно реагировать на последующие активации голосовой командой (после того, как приложение запускалось по меньшей мере один раз и были установлены наборы голосовых команд).

1.  Подтвердите, что приложение было активировано голосовой командой.

    Переопределите событие [**Application.OnActivated**](https://msdn.microsoft.com/library/windows/apps/br242330) и убедитесь, что [**IActivatedEventArgs**](https://msdn.microsoft.com/library/windows/apps/br224727).[**Kind**](https://msdn.microsoft.com/library/windows/apps/br224728) имеет значение [**VoiceCommand**](https://msdn.microsoft.com/library/windows/apps/br224693).

2.  Определите имя команды и фразу, которая была произнесена.

    Получите ссылку на объект [**VoiceCommandActivatedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn609755) из [**IActivatedEventArgs**](https://msdn.microsoft.com/library/windows/apps/br224727) и запросите свойство [**Result**](https://msdn.microsoft.com/library/windows/apps/dn609758) для объекта [**SpeechRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/dn631432).

    Чтобы определить, что было произнесено, проверьте значение [**Text**](https://msdn.microsoft.com/library/windows/apps/dn631441) или семантические свойства распознанной фразы в словаре [**SpeechRecognitionSemanticInterpretation**](https://msdn.microsoft.com/library/windows/apps/dn631443).

3.  Выполните соответствующее действие в приложении, например переход на нужную страницу.

В этом примере мы вернемся к VCD-файлу из шага 3 "Редактирование VCD-файла".

Получив результат распознавания речи для голосовой команды, мы получаем имя команды из первого значения в массиве [**RulePath**](https://msdn.microsoft.com/library/windows/apps/dn631438). Так как в VCD-файле определено несколько возможных голосовых команд, нужно сравнить значение с именами команд в VCD-файле и затем выполнить соответствующее действие.

Наиболее распространенным действием из тех, которые выполняются приложениями, является переход к странице, содержимое которой соответствует контексту голосовой команды. В нашем примере выполняется переход на страницу **TripPage**, и мы передаем значение голосовой команды, способ ее произнесения и распознанную фразу назначения (если применимо). Либо приложение может отправить навигационный параметр в [**SpeechRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/dn631432) при переходе на страницу.

Узнать о том, была ли голосовая команда, запустившая приложение, произнесена или введена как текст, можно при помощи ключа **commandMode** из словаря [**SpeechRecognitionSemanticInterpretation.Properties**](https://msdn.microsoft.com/library/windows/apps/dn631445). Значение этого ключа будет либо «voice», либо «text». Если значение ключа — "voice", попробуйте воспользоваться синтезом речи ([**Windows.Media.SpeechSynthesis**](https://msdn.microsoft.com/library/windows/apps/dn278951)) в приложении, чтобы предоставить пользователю голосовой отзыв.

Используйте [**SpeechRecognitionSemanticInterpretation.Properties**](https://msdn.microsoft.com/library/windows/apps/dn631445), чтобы определить произносимое содержимое в ограничениях **PhraseList** или **PhraseTopic** элемента **ListenFor**. Ключом словаря является значение атрибута **Label** элемента **PhraseList** или **PhraseTopic**. Здесь мы покажем, как получить доступ к значению фразы **{destination}**.

``` csharp
/// <summary>
/// Entry point for an application activated by some means other than normal launching. 
/// This includes voice commands, URI, share target from another app, and so on. 
/// 
/// NOTE:
/// A previous version of the VCD file might remain in place 
/// if you modify it and update the app through the store. 
/// Activations might include commands from older versions of your VCD. 
/// Try to handle these commands gracefully.
/// </summary>
/// <param name="args">Details about the activation method.</param>
protected override void OnActivated(IActivatedEventArgs args)
{
    base.OnActivated(args);

    Type navigationToPageType;
    ViewModel.TripVoiceCommand? navigationCommand = null;

    // Voice command activation.
    if (args.Kind == ActivationKind.VoiceCommand)
    {
        // Event args can represent many different activation types. 
        // Cast it so we can get the parameters we care about out.
        var commandArgs = args as VoiceCommandActivatedEventArgs;

        Windows.Media.SpeechRecognition.SpeechRecognitionResult speechRecognitionResult = commandArgs.Result;

        // Get the name of the voice command and the text spoken. 
        // See VoiceCommands.xml for supported voice commands.
        string voiceCommandName = speechRecognitionResult.RulePath[0];
        string textSpoken = speechRecognitionResult.Text;

        // commandMode indicates whether the command was entered using speech or text.
        // Apps should respect text mode by providing silent (text) feedback.
        string commandMode = this.SemanticInterpretation("commandMode", speechRecognitionResult);
        
        switch (voiceCommandName)
        {
            case "showTripToDestination":
                // Access the value of {destination} in the voice command.
                string destination = this.SemanticInterpretation("destination", speechRecognitionResult);

                // Create a navigation command object to pass to the page. 
                navigationCommand = new ViewModel.TripVoiceCommand(
                    voiceCommandName,
                    commandMode,
                    textSpoken,
                    destination);

                // Set the page to navigate to for this voice command.
                navigationToPageType = typeof(View.TripDetails);
                break;
            default:
                // If we can't determine what page to launch, go to the default entry point.
                navigationToPageType = typeof(View.TripListView);
                break;
        }
    }
    // Protocol activation occurs when a card is clicked within Cortana (using a background task).
    else if (args.Kind == ActivationKind.Protocol)
    {
        // Extract the launch context. In this case, we're just using the destination from the phrase set (passed
        // along in the background task inside Cortana), which makes no attempt to be unique. A unique id or 
        // identifier is ideal for more complex scenarios. We let the destination page check if the 
        // destination trip still exists, and navigate back to the trip list if it doesn't.
        var commandArgs = args as ProtocolActivatedEventArgs;
        Windows.Foundation.WwwFormUrlDecoder decoder = new Windows.Foundation.WwwFormUrlDecoder(commandArgs.Uri.Query);
        var destination = decoder.GetFirstValueByName("LaunchContext");

        navigationCommand = new ViewModel.TripVoiceCommand(
                                "protocolLaunch",
                                "text",
                                "destination",
                                destination);

        navigationToPageType = typeof(View.TripDetails);
    }
    else
    {
        // If we were launched via any other mechanism, fall back to the main page view.
        // Otherwise, we'll hang at a splash screen.
        navigationToPageType = typeof(View.TripListView);
    }

    // Repeat the same basic initialization as OnLaunched() above, taking into account whether
    // or not the app is already active.
    Frame rootFrame = Window.Current.Content as Frame;

    // Do not repeat app initialization when the Window already has content,
    // just ensure that the window is active.
    if (rootFrame == null)
    {
        // Create a frame to act as the navigation context and navigate to the first page.
        rootFrame = new Frame();
        App.NavigationService = new NavigationService(rootFrame);

        rootFrame.NavigationFailed += OnNavigationFailed;

        // Place the frame in the current window.
        Window.Current.Content = rootFrame;
    }

    // Since we're expecting to always show a details page, navigate even if 
    // a content frame is in place (unlike OnLaunched).
    // Navigate to either the main trip list page, or if a valid voice command
    // was provided, to the details page for that trip.
    rootFrame.Navigate(navigationToPageType, navigationCommand);

    // Ensure the current window is active
    Window.Current.Activate();
}

/// <summary>
/// Returns the semantic interpretation of a speech result. 
/// Returns null if there is no interpretation for that key.
/// </summary>
/// <param name="interpretationKey">The interpretation key.</param>
/// <param name="speechRecognitionResult">The speech recognition result to get the semantic interpretation from.</param>
/// <returns></returns>
private string SemanticInterpretation(string interpretationKey, SpeechRecognitionResult speechRecognitionResult)
{
  return speechRecognitionResult.SemanticInterpretation.Properties[interpretationKey].FirstOrDefault();
}
```

## <span id="Handle_the_voice_command_in_the_app_service"></span><span id="handle_the_voice_command_in_the_app_service"></span><span id="HANDLE_THE_VOICE_COMMAND_IN_THE_APP_SERVICE"></span>Обработка голосовой команды в службе приложения


Обработка голосовой команды в службе приложения.


1.  Используя директивы, добавьте следующее в файл службы голосовых команд (например, AdventureWorksVoiceCommandService.cs).
```csharp
using Windows.ApplicationModel.VoiceCommands;
using Windows.ApplicationModel.Resources.Core;
using Windows.ApplicationModel.AppService;
```

2.  Возьмите отсрочку службы, чтобы служба приложения не завершала работу во время обработки голосовой команды.
3.  Подтвердите, что фоновая задача выполняется как служба приложения, активированная голосовой командой.

    1.  Приведите объект [**IBackgroundTaskInstance.TriggerDetails**](https://msdn.microsoft.com/library/windows/apps/br224802) к типу [**Windows.ApplicationModel.AppService.AppServiceTriggerDetails**](https://msdn.microsoft.com/library/windows/apps/dn921727).
    2.  Убедитесь, что имя службы приложения в файле Package.appxmanifest — [**IBackgroundTaskInstance.TriggerDetails.Name**](https://msdn.microsoft.com/library/windows/apps/br224807).

4.  Используйте [**IBackgroundTaskInstance.TriggerDetails**](https://msdn.microsoft.com/library/windows/apps/br224802), чтобы создать объект [**VoiceCommandServiceConnection**](https://msdn.microsoft.com/library/windows/apps/dn974204) в **Кортане** для получения голосовой команды.
5.  Зарегистрируйте обработчик событий для события [**VoiceCommandServiceConnection**](https://msdn.microsoft.com/library/windows/apps/dn974204).[**VoiceCommandCompleted**](https://msdn.microsoft.com/library/windows/apps/dn706584), чтобы получить уведомление, когда служба приложения закроется из-за отмены пользователем.
6.  Зарегистрируйте обработчик событий для события [**IBackgroundTaskInstance.Canceled**](https://msdn.microsoft.com/library/windows/apps/br224798), чтобы получить уведомление, когда служба приложения закроется из-за неожиданного сбоя.
7.  Определите имя команды и фразу, которая была произнесена.

    1.  Используйте свойство [**VoiceCommand**](https://msdn.microsoft.com/library/windows/apps/dn974162).[**CommandName**](https://msdn.microsoft.com/library/windows/apps/dn706589), чтобы определить имя голосовой команды.
    2.  Чтобы определить, что было произнесено, проверьте значение [**Text**](https://msdn.microsoft.com/library/windows/apps/dn631441) или контекстные свойства распознанной фразы в словаре [**SpeechRecognitionSemanticInterpretation**](https://msdn.microsoft.com/library/windows/apps/dn631443).

7.  Выполните подходящее действие в своей службе приложения.
8.  Отобразите и произнесите соответствующий отзыв о голосовой команде в **Кортане**.

    1.  Определите строки, которые **Кортана** должна отобразить и произнести пользователю в ответ на голосовую команду, и создайте объект [**VoiceCommandResponse**](https://msdn.microsoft.com/library/windows/apps/dn974182). Инструкции по выбору строк обратной связи, которые **Кортана** отображает и произносит, см. в [рекомендациях по проектированию Кортаны](https://msdn.microsoft.com/library/windows/apps/dn974233).
    2.  Используйте экземпляр [**VoiceCommandServiceConnection**](https://msdn.microsoft.com/library/windows/apps/dn974204), чтобы сообщать **Кортане** о выполнении или завершении, вызывая команду [**ReportProgressAsync**](https://msdn.microsoft.com/library/windows/apps/dn706579) или [**ReportSuccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn706580) с помощью объекта **VoiceCommandServiceConnection**.

    Например, вернемся к VCD-файлу на шаге 3 "Редактирование VCD-файла".

```csharp
public sealed class VoiceCommandService : IBackgroundTask
    {
      private BackgroundTaskDeferral serviceDeferral;
      VoiceCommandServiceConnection voiceServiceConnection;

      public async void Run(IBackgroundTaskInstance taskInstance)
      {
      //Take a service deferral so the service isn&#39;t terminated.
        this.serviceDeferral = taskInstance.GetDeferral();

        taskInstance.Canceled += OnTaskCanceled;

        var triggerDetails = 
          taskInstance.TriggerDetails as AppServiceTriggerDetails;

        if (triggerDetails != null &amp;&amp; 
          triggerDetails.Name == "AdventureWorksVoiceServiceEndpoint")
        {
          try
          {
 voiceServiceConnection = 
   VoiceCommandServiceConnection.FromAppServiceTriggerDetails(
     triggerDetails);

 voiceServiceConnection.VoiceCommandCompleted += 
   VoiceCommandCompleted;

 VoiceCommand voiceCommand = await
 voiceServiceConnection.GetVoiceCommandAsync();

 switch (voiceCommand.CommandName)
 {
   case "whenIsTripToDestination":
   {
     var destination = 
       voiceCommand.Properties["destination"][0];
     SendCompletionMessageForDestination(destination);
     break;
   }

   // As a last resort, launch the app in the foreground.
   default:
     LaunchAppInForeground();
     break;
 }
          }
          finally
          {
 if (this.serviceDeferral != null)
 {
   // Complete the service deferral.
   this.serviceDeferral.Complete();
 }
          }
        }
      }

      private void VoiceCommandCompleted(
        VoiceCommandServiceConnection sender, 
        VoiceCommandCompletedEventArgs args)
      {
        if (this.serviceDeferral != null)
        {
          // Insert your code here.
          // Complete the service deferral.
          this.serviceDeferral.Complete();
        }
      }

      private async void SendCompletionMessageForDestination(
        string destination)
      {
        // Take action and determine when the next trip to destination
        // Insert code here.
        
        // Replace the hardcoded strings used here with strings 
        // appropriate for your application.

        // First, create the VoiceCommandUserMessage with the strings 
        // that Cortana will show and speak.
        var userMessage = new VoiceCommandUserMessage();
        userMessage.DisplayMessage = "Here’s your trip.";
        userMessage.SpokenMessage = "Your trip to Vegas is on August 3rd.";

        // Optionally, present visual information about the answer.
        // For this example, create a VoiceCommandContentTile with an 
        // icon and a string.
        var destinationsContentTiles = new List<VoiceCommandContentTile>();

        var destinationTile = new VoiceCommandContentTile();
        destinationTile.ContentTileType = 
          VoiceCommandContentTileType.TitleWith68x68IconAndText;
        // The user can tap on the visual content to launch the app. 
        // Pass in a launch argument to enable the app to deep link to a 
        // page relevant to the item displayed on the content tile.
        destinationTile.AppLaunchArgument = 
          string.Format("destination={0}”, “Las Vegas");
        destinationTile.Title = "Las Vegas";
        destinationTile.TextLine1 = "August 3rd 2015";
        destinationsContentTiles.Add(destinationTile);

        // Create the VoiceCommandResponse from the userMessage and list    
        // of content tiles.
        var response = 
          VoiceCommandResponse.CreateResponse(
 userMessage, destinationsContentTiles);

        // Cortana will present a “Go to app_name” link that the user 
        // can tap to launch the app. 
        // Pass in a launch to enable the app to deep link to a page 
        // relevant to the voice command.
        response.AppLaunchArgument = 
          string.Format("destination={0}”, “Las Vegas");
        
        // Ask Cortana to display the user message and content tile and 
        // also speak the user message.
        await voiceServiceConnection.ReportSuccessAsync(response);
      }

      private async void LaunchAppInForeground()
      {
        var userMessage = new VoiceCommandUserMessage();
        userMessage.SpokenMessage = "Launching Adventure Works";

        var response = VoiceCommandResponse.CreateResponse(userMessage);

        // When launching the app in the foreground, pass an app 
        // specific launch parameter to indicate what page to show.
        response.AppLaunchArgument = "showAllTrips=true";

        await voiceServiceConnection.RequestAppLaunchAsync(response);
      }
    }
```

После активации у службы приложения есть 0,5 секунды, чтобы вызвать команду [**ReportSuccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn706580). **Кортана** использует данные, предоставленные приложением, чтобы отобразить или произнести отзыв, который определен в VCD-файле. Если приложение осуществляет вызов дольше 0,5 секунды, **Кортана** вставляет экран передачи, как показано ниже. **Кортана** отображает экран передачи, пока приложение вызывает команду **ReportSuccessAsync**, или до 5 секунд. Если служба приложения не вызывает команду **ReportSuccessAsync** или какой-либо метод [**VoiceCommandServiceConnection**](https://msdn.microsoft.com/library/windows/apps/dn974204), предоставляющий **Кортане** сведения, пользователь получает сообщение об ошибке, а действие службы приложения отменяется.

![Базовый запрос с экранами выполнения и результатов, который использует приложение Adventure Works в фоновом режиме](images/cortana-backgroundapp-progress-result.png)

## <span id="related_topics"></span>Связанные разделы


**Разработчикам**
* [Взаимодействие с Кортаной](cortana-interactions.md)
* [Определение настраиваемых ограничений распознавания](define-custom-recognition-constraints.md)
* [Взаимодействие с фоновым приложением в Кортане](interact-with-a-background-app-in-cortana.md)
* [**Элементы и атрибуты VCD вер. 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593)
* [Краткое руководство: использование файловых и графических ресурсов](https://msdn.microsoft.com/library/windows/apps/xaml/hh965325)
* [Как присваивать имена ресурсам с помощью квалификаторов](https://msdn.microsoft.com/library/windows/apps/xaml/hh965324)

**Проектировщикам**
* [Рекомендации по проектированию Кортаны](https://msdn.microsoft.com/library/windows/apps/dn974233)
* [Рекомендации по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121)
* [Адаптивный дизайн 101 для приложений UWP](https://msdn.microsoft.com/library/windows/apps/dn958435)
* [Руководство по работе с ресурсами плиток и значков](https://msdn.microsoft.com/library/windows/apps/mt412102)

**Примеры**
* [Пример голосовой команды Кортаны](http://go.microsoft.com/fwlink/p/?LinkID=619899)
 

 







<!--HONumber=Jun16_HO3-->


