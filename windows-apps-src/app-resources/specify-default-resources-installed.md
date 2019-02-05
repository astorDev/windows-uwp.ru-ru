---
Description: If your app doesn't have resources that match the particular settings of a customer device, then the app's default resources are used. This topic explains how to specify what those default resources are.
title: Указание ресурсов по умолчанию, которые используются в приложении
template: detail.hbs
ms.date: 11/14/2017
ms.topic: article
keywords: Windows 10, uwp, ресурс, изображение, средство, MRT, квалификатор
ms.localizationpriority: medium
ms.openlocfilehash: b11f6dfec2941ae4eaa277f37de66965bd1ea4aa
ms.sourcegitcommit: bf600a1fb5f7799961914f638061986d55f6ab12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2019
ms.locfileid: "9048907"
---
# <a name="specify-the-default-resources-that-your-app-uses"></a>Указание ресурсов по умолчанию, которые используются в приложении

Если у приложения нет ресурсов, которые соответствуют определенным параметрам устройства клиента, используются ресурсы приложения по умолчанию. В этом разделе описывается, как указать эти ресурсы по умолчанию.

Когда пользователь устанавливает ваше приложение из Microsoft Store, параметры на устройстве пользователя сопоставляются с доступными ресурсами приложения. Это сопоставление выполняется так, чтобы скачать и установить только соответствующие ресурсы. Например, используются наиболее подходящие строки и изображения в соответствии с языковыми предпочтениями пользователя, разрешением устройства и экрана. Например, `200` —это значение по умолчанию для `scale`, но при желании его можно изменить.

Даже для ресурсов, которые не добавляются в собственные пакеты ресурсов (например, изображения, адаптированные для высокой контрастности), можно указать, какие ресурсы по умолчанию приложение должно использовать во время выполнения, если ресурс, соответствующий параметрам пользователя, не найден. Например, `standard` —это значение по умолчанию для `contrast`, но при желании его можно изменить.

Эти значения по умолчанию указывается в виде значений квалификатора ресурсов по умолчанию. Описание квалификаторов ресурсов и их использования см. в разделе [Адаптация ресурсов с учетом языка, масштаба, высокой контрастности и других квалификаторов](tailor-resources-lang-scale-contrast.md).

Вы можете настроить эти значения по умолчанию двумя способами. Вы можете добавить файл конфигурации в проект или изменить файл проекта напрямую. Используйте любой из этих способов, который кажется вам более удобным или который лучше всего подходит для системы сборки.

## <a name="option-1-use-priconfigdefaultxml-to-specify-default-qualifier-values"></a>Вариант 1. Использование файла priconfig.default.xml для указания значений квалификатора по умолчанию

1. Добавьте новый элемент в проект в Visual Studio. Выберите XML-файл и укажите для него имя `priconfig.default.xml`.
2. В обозревателе решений выберите `priconfig.default.xml` и откройте окно "Свойства". Параметру "Действие при сборке" файла должно быть задано значение "Нет", а параметру "Копировать в выходной каталог" — "Не копировать".
3. Замените содержимое файла этим XML-кодом.
   ```xml
   <default>
      <qualifier name="Language" value="LANGUAGE-TAG(S)" />
      <qualifier name="Contrast" value="standard" />
      <qualifier name="Scale" value="200" />
      <qualifier name="HomeRegion" value="001" />
      <qualifier name="TargetSize" value="256" />
      <qualifier name="LayoutDirection" value="LTR" />
      <qualifier name="DXFeatureLevel" value="DX9" />
      <qualifier name="Configuration" value="" />
      <qualifier name="AlternateForm" value="" />
   </default>
   ```
   
   **Примечание.** Значение `LANGUAGE-TAG(S)` необходимо синхронизировать с языком приложения по умолчанию. Если это один [тег языка BCP-47](https://go.microsoft.com/fwlink/p/?linkid=227302), то язык приложения по умолчанию должен быть определен тем же тегом. Если это разделенный запятыми список языковых тегов, язык приложения по умолчанию должен быть первым тегом в списке. Язык по умолчанию указывается в поле **Язык по умолчанию** вашего приложения на вкладке **Приложение** в исходном файле манифеста пакета приложения (`Package.appxmanifest`).

4. Каждый элемент `<qualifier>` сообщает Visual Studio, какое значение следует использовать по умолчанию для каждого имени квалификатора. Текущее содержимое файла не меняет поведение Visual Studio. Другими словами, среда Visual Studio *уже работала так, если бы* этот файл с этим содержимым уже существовал, так как эти значения используются по умолчанию. Чтобы переопределить значение по умолчанию значение, необходимо изменить значение в файле. Вот пример того, как может выглядеть файл после изменения первых трех значений.
   ```xml
   <default>
      <qualifier name="Language" value="de-DE" />
      <qualifier name="Contrast" value="black" />
      <qualifier name="Scale" value="400" />
      <qualifier name="HomeRegion" value="001" />
      <qualifier name="TargetSize" value="256" />
      <qualifier name="LayoutDirection" value="LTR" />
      <qualifier name="DXFeatureLevel" value="DX9" />
      <qualifier name="Configuration" value="" />
      <qualifier name="AlternateForm" value="" />
   </default>
   ```
5. Сохраните и закройте файл и заново выполните построение проекта.

Чтобы убедиться, что значения по умолчанию учитываются, изучите файл `<ProjectFolder>\obj\<ReleaseConfiguration folder>\priconfig.xml` и убедитесь, что его содержимое соответствует выбранным значениям. Если это так, вы успешно настроили значения квалификатора ресурсов, которые ваше приложение будет использовать по умолчанию. Если совпадение для параметров пользователя не найдено, будут использоваться ресурсы, имена папки или файла которых содержат значения квалификатора по умолчанию, указанные здесь.

### <a name="how-does-this-work"></a>Как это работает?

Visual Studio запускает средство `MakePri.exe` в фоновом режиме для создания файла, который называют индексом ресурсов пакета, описывающий все ресурсы вашего приложения, в том числе ресурсы по умолчанию. Сведения об этом средстве см. в разделе [Компиляция ресурсов вручную с помощью MakePri.exe](compile-resources-manually-with-makepri.md). Visual Studio передает файл конфигурации `MakePri.exe`. Содержимое файла `priconfig.default.xml` используется в качестве элемента `<default>` этого файла конфигурации, который определяет набор значений квалификаторов, используемых по умолчанию. Таким образом, добавление и изменение `priconfig.default.xml` влияет на содержимое файла индекса ресурсов пакетов, который Visual Studio создает для вашего приложения и добавляет в пакет приложения.

**Примечание.** Если вам потребуется изменить значение элемента `<qualifier name="Language" ... />`, вам необходимо синхронизировать это изменение с языком приложения по умолчанию. Это требуется, чтобы языковые ресурсы, индексируемые в PRI-файле приложения, соответствовали языку манифеста по умолчанию. Значение в элементе `<qualifier name="Language" ... />` переопределяет значение в манифесте относительно содержимого `<ProjectFolder>\obj\<ReleaseConfiguration folder>\priconfig.xml`, но при этом файл и манифест приложения должны совпадать.

### <a name="using-a-different-file-name-than-priconfigdefaultxml"></a>Использование имени, отличного от `priconfig.default.xml`

Если назвать файл `priconfig.default.xml`, Visual Studio распознает его и будет использовать его автоматически. Если указать другое имя, необходимо сообщить об этом Visual Studio. Добавьте этот XML-код в файл проекта между открывающим и закрывающим тегами первого элемента `<PropertyGroup>`.

```xml
<AppxPriConfigXmlDefaultSnippetPath>FILE-PATH-AND-NAME</AppxPriConfigXmlDefaultSnippetPath>
```

Замените `FILE-PATH-AND-NAME` путем к файлу и именем файла.

## <a name="option-2-use-your-project-file-to-specify-default-qualifier-values"></a>Вариант 2. Использование файла проекта для указания значений квалификатора по умолчанию

Это альтернатива варианту 1. Если вы поняли, как работает вариант 1, вы можете выбрать вариант 2, если он больше подходит для вас.

Добавьте этот XML-код в файл проекта между открывающим и закрывающим тегами первого элемента `<PropertyGroup>`.

```xml
<AppxDefaultResourceQualifiers>Language=LANGUAGE-TAG(S)|Contrast=standard|Scale=200|HomeRegion=001|TargetSize=256|LayoutDirection=LTR|DXFeatureLevel=DX9|Configuration=|AlternateForm=</AppxDefaultResourceQualifiers>
```

Вот пример того, как может выглядеть файл после изменения первых трех значений.

```xml
<AppxDefaultResourceQualifiers>Language=de-DE|Contrast=black|Scale=400|HomeRegion=001|TargetSize=256|LayoutDirection=LTR|DXFeatureLevel=DX9|Configuration=|AlternateForm=</AppxDefaultResourceQualifiers>
```

Сохраните и закройте файл и заново выполните построение проекта.

**Примечание.** Если вам потребуется изменить значение `Language=`, вам необходимо синхронизировать это изменение с языком приложения по умолчанию в конструкторе манифеста (открыв `Package.appxmanifest`).

## <a name="related-topics"></a>Статьи по теме

* [Адаптация ресурсов с учетом языка, масштаба, высокой контрастности и других квалификаторов](tailor-resources-lang-scale-contrast.md)
* [Тег языка BCP-47](https://go.microsoft.com/fwlink/p/?linkid=227302)
* [Компиляция ресурсов вручную с помощью MakePri.exe](compile-resources-manually-with-makepri.md)
