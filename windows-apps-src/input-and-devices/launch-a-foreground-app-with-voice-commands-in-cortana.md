---
Description: Помимо использования голосовых команд Кортаны для получения доступа к системным компонентам, можно также использовать голосовые команды Кортаны для запуска приложения переднего плана и указания действия или команды, которые следует выполнить в приложении.
title: Запуск приложения переднего плана с помощью голосовых команд в Кортане
ms.assetid: 8D3D1F66-7D17-4DD1-B426-DCCBD534EF00
label: Cortana-Launch a foreground app
template: detail.hbs
---

# Активация приложения переднего плана с помощью голосовых команд в Кортане


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**Windows.ApplicationModel.VoiceCommands**](https://msdn.microsoft.com/library/windows/apps/dn706594)
-   [**Элементы и атрибуты VCD вер. 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593)

Помимо использования голосовых команд в **Кортане** для доступа к системным функциям можно дополнить возможности **Кортаны** функциональностью вашего приложения. С помощью голосовых команд можно активировать приложение на переднем плане или выполнить в приложении определенное действие или команду. 

Если приложение обрабатывает голосовую команду на переднем плане, оно находится в фокусе, а Кортана закрывается. При необходимости можно активировать приложение и выполнить команду в фоновом режиме. В этом случае Кортана остается в фокусе, а приложение возвращает обратную связь и результаты на холсте **Кортаны** и с помощью голосовых функций **Кортаны**.

Голосовые команды, для которых требуется дополнительный контекст или пользовательский ввод (такой как отправка сообщения определенному контакту), наилучшим образом обрабатываются на переднем плане, а основные команды (перечисление предстоящих поездок) при этом могут обрабатываться в **Кортане** в фоновом приложении.

Если требуется активировать приложение в фоновом режиме с помощью голосовых команд, см. инструкции в разделе [Активация фонового приложения с помощью голосовых команд Кортаны](launch-a-background-app-with-voice-commands-in-cortana.md).

> **Примечание.**  
> Голосовая команда — это одно изречение, имеющее конкретную цель, определенное в файле определения голосовых команд (VCD) и направленное на установленное приложение через **Кортану**.

> VCD-файл определяет одну или несколько голосовых команд, каждая из которых несет в себе уникальное намерение.

> Определение голосовых команд может иметь разную сложность. Определение может поддерживать от одного ограниченного изречения до набора более гибких, свойственных языку, изречений, которые имеют один и тот же смысл.

Чтобы продемонстрировать функции приложения переднего плана, воспользуемся приложением для планирования поездок и управления ими под названием **Adventure Works** из [примера голосовой команды Кортаны](http://go.microsoft.com/fwlink/p/?LinkID=619899).

Чтобы создать новую поездку **Adventure Works** без использования **Кортаны**, пользователю нужно было бы запустить приложение и перейти на страницу **Новая поездка**. Чтобы просмотреть существующую поездку, пользователь должен запустить приложение, перейти на страницу **Предстоящие поездки** и выбрать необходимую поездку.

Используя голосовые команды в **Кортане**, пользователь вместо этого может просто сказать «Adventure Works, добавь поездку» или «Добавить поездку в Adventure Works» для запуска приложения и перехода к странице **Новая поездка**. В свою очередь, фраза «Adventure Works, покажи мою поездку в Лондон» запустит приложение и откроет страницу подробностей **Поездка**, показанную ниже.

![Кортана запускает приложение переднего плана](images/cortana-foreground-with-adventureworks.png)

Далее перечислены основные шаги по добавлению функций голосовых команд и интеграции Кортаны с вашим приложением при помощи речевого ввода или ввода с клавиатуры.

1.  Создайте VCD-файл. Это XML-документ, определяющий все фразы, произносимые пользователем для выполнения действий или команд при активации приложения. См. раздел [**Элементы и атрибуты VCD вер. 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593).
2.  Зарегистрируйте наборы команд в VCD-файле при запуске приложения.
3.  Обработайте активацию голосовой командой, навигацию в приложении и выполнение команды.

**Необходимые условия**

Если вы — начинающий разработчик приложений универсальной платформы Windows (UWP), ознакомьтесь со следующими разделами, чтобы получить представление об описываемых здесь технологиях.

-   [Создание первого приложения](https://msdn.microsoft.com/library/windows/apps/bg124288)
-   Дополнительную информацию о событиях см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/library/windows/apps/mt185584).

**Рекомендации по взаимодействию с пользователем: **

Сведения об интеграции приложения с **Кортаной** см. в [рекомендациях по проектированию Кортаны](https://msdn.microsoft.com/library/windows/apps/dn974233), а полезные советы по проектированию удобного и привлекательного приложения с поддержкой распознавания речи — в [рекомендациях по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121).

## <span id="Create_a_new_solution_with_project_in_Visual_Studio"></span><span id="create_a_new_solution_with__project_in_visual_studio"></span><span id="CREATE_A_NEW_SOLUTION_WITH__PROJECT_IN_VISUAL_STUDIO"></span>Создание решения с проектом в Visual Studio


1.  Запустите Microsoft Visual Studio 2015.

    Откроется начальный экран Visual Studio 2015.

2.  В меню **Файл** выберите команду **Создать** > **Проект**.

    Откроется диалоговое окно **Создать проект**. Левая панель диалогового окна позволяет выбрать тип отображаемого шаблона.

3.  На левой панели разверните узел **Установленные > Шаблоны > Visual C\# > Windows** и выберите группу шаблонов **Универсальные**. На центральной панели диалогового окна отображается список шаблонов проектов для приложений универсальной платформы Windows (UWP).
4.  На центральной панели выберите шаблон **Пустое приложение (универсальное приложение для Windows)**.

    Шаблон **Пустое приложение** дает возможность создать обладающее минимальным функционалом приложение UWP, которое компилируется и запускается, но не содержит данных или элементов управления пользовательского интерфейса. Вы будете добавлять элементы управления и данные в процессе работы с этим руководством.

5.  В текстовом поле **Имя** введите имя вашего проекта. В этом примере мы используем имя "AdventureWorks".
6.  Чтобы создать проект, нажмите кнопку **ОК**.

    Microsoft Visual Studio создаст проект, который появится в **обозревателе решений**.

## <span id="Add_image_assets_to_project_and_specify_them_in_the_app_manifest"></span><span id="add_image_assets_to_project_and_specify_them_in_the_app_manifest"></span><span id="ADD_IMAGE_ASSETS_TO_PROJECT_AND_SPECIFY_THEM_IN_THE_APP_MANIFEST"></span>Добавление ресурсов изображений в проект и указание их в манифесте приложения
      
Приложения UWP могут автоматически выбирать наиболее подходящие изображения на основе определенных параметров и возможностей устройства (высокая контрастность, эффективные пиксели, язык и т. д.). Достаточно предоставить изображения, придерживаясь соответствующего соглашения об именовании и используя правильную структуру папок в проекте приложения для различных версий ресурсов. Если не предоставить рекомендуемые версии ресурсов, это может отрицательно сказаться на специальных возможностях, качестве локализации и изображений в зависимости от пользовательских настроек, возможностей, типа устройства и местоположения.

Дополнительную информацию о ресурсах изображений для высокой контрастности и коэффициентов масштабирования см. в статье [Руководство по работе с ресурсами плиток и значков](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets).

Назовите ресурсы с помощью квалификаторов. Квалификаторы ресурсов — это модификаторы папки и имени файла, которые определяют контекст использования версии ресурса.

Стандартное соглашение об именовании — это `foldername/qualifiername-value[_qualifiername-value]/filename.qualifiername-value[_qualifiername-value].ext`. Например, объект `images/en-US/logo.scale-100_contrast-white.png`, на который легко сослаться в коде, указав корневую папку и имя файла: `images/logo.png`. См. раздел [Как присваивать имена ресурсам с помощью квалификаторов](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/hh965324.aspx).

Рекомендуется пометить язык по умолчанию и строковые файлы ресурсов (например, `en-US\resources.resw`), а также стандартный коэффициент масштабирования изображений (например, `logo.scale-100.png`), даже если вы не планируете в настоящий момент предоставлять локализованные ресурсы или ресурсы с разными разрешениями. Тем не менее, мы настоятельно рекомендуем предоставлять ресурсы с коэффициентами масштабирования 100, 200 и 400.

> *Важно!

> Значок приложения, используемый в области названия холста **Кортаны** — это значок Square44x44Logo, определенный в файле "Package.appxmanifest". 
    
## <span id="Create_a_VCD_file"></span><span id="create_a_vcd_file"></span><span id="CREATE_A_VCD_FILE"></span>Создание VCD-файла

1. В Visual Studio щелкните правой кнопкой мыши имя основного проекта и выберите **Добавить > Новый элемент**. Добавьте **XML-файл**.
2. Введите имя [**VCD-файла**](https://msdn.microsoft.com/library/windows/apps/dn706593) (например, AdventureWorksCommands.xml) и нажмите кнопку "Добавить". 
3. В **обозревателе решений** выберите файл [**VCD**](https://msdn.microsoft.com/library/windows/apps/dn706593).
4.  В окне **Свойства** задайте для элемента **Действие при сборке** значение **Содержимое**, а для элемента **Копировать в выходной каталог** — значение **Копировать более позднюю версию**.

## <span id="Edit_the_VCD_file"></span><span id="edit_the_vcd_file"></span><span id="EDIT_THE_VCD_FILE"></span>Редактирование VCD-файла


. Добавьте элемент **VoiceCommands** с атрибутом **xmlns**, указывающим на адрес http://schemas.microsoft.com/voicecommands/1.2.

2. Для каждого языка, поддерживаемого приложением, создайте элемент [**CommandSet**](https://msdn.microsoft.com/library/windows/apps/dn722331), который содержит голосовые команды, поддерживаемые приложением.

  Можно объявить несколько элементов [**CommandSet**](https://msdn.microsoft.com/library/windows/apps/dn722331) с разными атрибутами [**xml:lang**](https://msdn.microsoft.com/library/windows/apps/dn722331), чтобы приложение было пригодно к использованию на различных рынках. Например, приложение для США может иметь элемент [**CommandSet**](https://msdn.microsoft.com/library/windows/apps/dn722331) для английского языка и элемент [**CommandSet**](https://msdn.microsoft.com/library/windows/apps/dn722331) для испанского.

  >  **Внимание!**  
  Для активации приложения и выполнения действия с помощью голосовой команды приложение должно зарегистрировать VCD-файл, содержащий элемент [**CommandSet**](https://msdn.microsoft.com/library/windows/apps/dn722331) с языком, который совпадает с выбранным пользователем языком голосовых функций на устройстве. Язык голосовых функций находится в папке **Параметры > Система > Голосовые функции > Язык голосовых функций**.

3. Добавьте элемент **Command** для каждой команды, поддержку которой необходимо обеспечить.

  Каждая команда (**Command**), объявленная в файле [**VCD**](https://msdn.microsoft.com/library/windows/apps/dn706593), должна содержать приведенную ниже информацию.

  - Атрибут **Name**, используемый вашим приложением для идентификации голосовой команды в среде выполнения. 
  - Элемент **Example**, который содержит фразу, описывающую возможные способы вызова команды пользователем. **Кортана** показывает этот пример, когда пользователь говорит «Что можно говорить?», «Помоги» или касается пункта **Подробнее**.    
  -   Элемент **ListenFor**, содержащий слова или фразы, которые приложение распознает в качестве команды. Каждый элемент **ListenFor** может содержать ссылки на один или несколько элементов **PhraseList** с конкретными словами, связанными с этой командой.
  > **Примечание.**  
Элементы   **ListenFor** невозможно изменить программно. Однако программно можно изменять элементы **PhraseList**, связанные с элементами **ListenFor**. Приложения должны изменять содержимое **PhraseList** во время выполнения на основе созданного набора данных, когда пользователь использует приложение. Ознакомьтесь со статьей [Динамическое изменение списков фраз определения голосовых команд (VCD)](dynamically-modify-voice-command-definition--vcd--phrase-lists.md).

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
```csharp
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
}```

## <span id="Handle_activation_and_execute_voice_commands"></span><span id="handle_activation_and_execute_voice_commands"></span><span id="HANDLE_ACTIVATION_AND_EXECUTE_VOICE_COMMANDS"></span>Handle activation and execute voice commands

Specify how your app responds to subsequent voice command activations (after it has been launched at least once and the voice command sets have been installed).

1.  Confirm that your app was activated by a voice command.

    Override the [**Application.OnActivated**](https://msdn.microsoft.com/library/windows/apps/br242330) event and check whether [**IActivatedEventArgs**](https://msdn.microsoft.com/library/windows/apps/br224727).[**Kind**](https://msdn.microsoft.com/library/windows/apps/br224728) is [**VoiceCommand**](https://msdn.microsoft.com/library/windows/apps/br224693).

2.  Determine the name of the command and what was spoken.

    Get a reference to a [**VoiceCommandActivatedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn609755) object from the [**IActivatedEventArgs**](https://msdn.microsoft.com/library/windows/apps/br224727) and query the [**Result**](https://msdn.microsoft.com/library/windows/apps/dn609758) property for a [**SpeechRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/dn631432) object.

    To determine what the user said, check the value of [**Text**](https://msdn.microsoft.com/library/windows/apps/dn631441) or the semantic properties of the recognized phrase in the [**SpeechRecognitionSemanticInterpretation**](https://msdn.microsoft.com/library/windows/apps/dn631443) dictionary.

3.  Take the appropriate action in your app, such as navigating to the desired page.

For this example, we refer back to the VCD in Step 3: Edit the VCD file.

Once we get the speech-recognition result for the voice command, we get the command name from the first value in the [**RulePath**](https://msdn.microsoft.com/library/windows/apps/dn631438) array. As the VCD file defined more than one possible voice command, we need to compare the value against the command names in the VCD and take the appropriate action.

The most common action an application can take is to navigate to a page with content relevant to the context of the voice command. For this example, we navigate to a **TripPage** page and pass in the value of the voice command, how the command was input, and the recognized "destination" phrase (if applicable). Alternatively, the app could send a navigation parameter to the [**SpeechRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/dn631432) when navigating to the page.

You can find out whether the voice command that launched your app was actually spoken, or whether it was typed in as text, from the [**SpeechRecognitionSemanticInterpretation.Properties**](https://msdn.microsoft.com/library/windows/apps/dn631445) dictionary using the **commandMode** key. The value of that key will be either "voice" or "text". If the value of the key is "voice", consider using speech synthesis ([**Windows.Media.SpeechSynthesis**](https://msdn.microsoft.com/library/windows/apps/dn278951)) in your app to provide the user with spoken feedback.

Use the [**SpeechRecognitionSemanticInterpretation.Properties**](https://msdn.microsoft.com/library/windows/apps/dn631445) to find out the content spoken in the **PhraseList** or **PhraseTopic** constraints of a **ListenFor** element. The dictionary key is the value of the **Label** attribute of the **PhraseList** or **PhraseTopic** element. Here, we show how to access the value of **{destination}** phrase.

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

## <span id="related_topics"></span>Статьи по теме


**Разработчикам**
* [Взаимодействие с Кортаной](cortana-interactions.md)
* [Определение настраиваемых ограничений распознавания](define-custom-recognition-constraints.md)
* [**Элементы и атрибуты в VCD-файле, версия 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593)

**Проектировщикам**
* [Рекомендации по проектированию Кортаны](https://msdn.microsoft.com/library/windows/apps/dn974233)
* [Рекомендации по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121)

**Примеры**
* [Пример голосовой команды Кортаны](http://go.microsoft.com/fwlink/p/?LinkID=619899)
 

 






<!--HONumber=Mar16_HO4-->


