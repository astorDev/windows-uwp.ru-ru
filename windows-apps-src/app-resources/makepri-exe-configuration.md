---
author: stevewhims
Description: This topic describes the schema of the MakePri.exe XML configuration file.
title: Файл конфигурации MakePri.exe
template: detail.hbs
ms.author: stwhi
ms.date: 10/18/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, ресурс, изображение, средство, MRT, квалификатор
ms.localizationpriority: medium
ms.openlocfilehash: 512880b7a7ea955a45697762cbbdb7f74ac70102
ms.sourcegitcommit: 1c6325aa572868b789fcdd2efc9203f67a83872a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "4749885"
---
# <a name="makepriexe-configuration-file"></a>Файл конфигурации MakePri.exe

В этом разделе описывается схема XML-файла конфигурации [MakePri.exe](compile-resources-manually-with-makepri.md); который также называют файлом конфигурации PRI. Средство MakePri.exe поддерживает [команду createconfig](makepri-exe-command-options.md#createconfig-command), которую можно использовать для создания нового инициализированного файла конфигурации PRI.

> [!NOTE]
> MakePri.exe устанавливается при проверке параметр **Windows SDK для управляемых приложений UWP** при установке пакета средств разработки программного обеспечения Windows. Он устанавливается на путь `%WindowsSdkDir%bin\<WindowsTargetPlatformVersion>\x64\makepri.exe` (а также в папки с именем для других архитектур). Например, `C:\Program Files (x86)\Windows Kits\10\bin\10.0.17713.0\x64\makepri.exe`.

В файле конфигурации PRI задается, какие ресурсы индексируются и каким образом. XML-файл конфигурации должен соответствовать следующей схеме.

```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema attributeFormDefault="unqualified" elementFormDefault="qualified" xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:element name="resources">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="packaging" maxOccurs="1" minOccurs="0">
          <xs:complexType>
            <xs:sequence>
              <xs:element name="autoResourcePackage" maxOccurs="unbounded" minOccurs="0">
                <xs:complexType>
                  <xs:attribute name="qualifier" type="xs:string" use="required" />
                </xs:complexType>
              </xs:element>
              <xs:element name="resourcePackage" maxOccurs="unbounded" minOccurs="0">
                <xs:complexType>
                  <xs:sequence>
                    <xs:element name="qualifierSet" maxOccurs="unbounded" minOccurs="0">
                      <xs:complexType>
                        <xs:attribute name="definition" type="xs:string" use="required" />
                      </xs:complexType>
                    </xs:element>
                  </xs:sequence>
                  <xs:attribute name="name" type="xs:string" use="required" />
                </xs:complexType>
              </xs:element>
            </xs:sequence>
          </xs:complexType>
        </xs:element>
        <xs:element maxOccurs="unbounded" name="index">
          <xs:complexType>
            <xs:sequence>
              <xs:element name="qualifiers" minOccurs="0" maxOccurs="unbounded">
                <xs:complexType>
                  <xs:sequence>
                    <xs:element minOccurs="1" maxOccurs="unbounded" name="qualifier">
                      <xs:complexType>
                        <xs:attribute name="name" type="xs:string" use="required" />
                        <xs:attribute name="value" type="xs:string" use="required" />
                      </xs:complexType>
                    </xs:element>
                  </xs:sequence>
                </xs:complexType>
              </xs:element>
              <xs:element name="default" minOccurs="0" maxOccurs="unbounded">
                <xs:complexType>
                  <xs:sequence>
                    <xs:element minOccurs="1" maxOccurs="unbounded" name="qualifier">
                      <xs:complexType>
                        <xs:attribute name="name" type="xs:string" use="required" />
                        <xs:attribute name="value" type="xs:string" use="required" />
                      </xs:complexType>
                    </xs:element>
                  </xs:sequence>
                </xs:complexType>
              </xs:element>
              <xs:element name="indexer-config" minOccurs="0" maxOccurs="unbounded">
                <xs:complexType>
                  <xs:sequence>
                    <xs:any minOccurs="0" maxOccurs="unbounded" processContents="skip"/>
                  </xs:sequence>
                  <xs:attribute name="type" type="xs:string" use="required" />
                  <xs:anyAttribute processContents="skip"/>
                </xs:complexType>
              </xs:element>
            </xs:sequence>
            <xs:attribute name="root" type="xs:string" use="required" />
            <xs:attribute name="startIndexAt" type="xs:string" use="required" />
          </xs:complexType>
        </xs:element>
      </xs:sequence>
      <xs:attribute name="isDeploymentMergeable" type="xs:boolean" use="optional" />
      <xs:attribute name="majorVersion" type="xs:positiveInteger" use="optional" />
      <xs:attribute name="targetOsVersion" type="xs:string" use="optional" />
    </xs:complexType>
  </xs:element>
</xs:schema>
```

- Элемент `default` задает контекст (язык, масштаб, контраст и т.д.), который следует использовать для разрешения ресурсов в случае, когда контекст времени выполнения не подходит для рассматриваемых ресурсов. Поскольку этот контекст задается во время сборки и не меняется, ресурсы разрешаются в соответствии с ним, когда создаются квалификаторы. Соответствующий результат сохраняется во время сборки. Для каждого квалификатора должно быть указано какое-либо значение. В разделе [ResourceContext](resource-management-system.md#resourcecontext) приводятся сведения о том, как выбираются ресурсы.
- Элемент `index` определяет отдельные этапы индексации ресурсов. Каждый этап индексации определяет, какие должны использоваться [индексаторы для форматов](makepri-exe-format-specific-indexers.md), и какие ресурсы должны подвергаться индексации.
- Элемент `qualifiers` задает начальные квалификаторы для первого файла или папки, которые наследуются остальными ресурсами. Каждый квалифицирующий элемент должен иметь допустимое имя и значение (см. раздел [Адаптация ресурсов с учетом языка, масштаба, высокой контрастности и других квалификаторов](tailor-resources-lang-scale-contrast.md)).
- Атрибут `root` задает корневой каталог пути к физическому файлу для этапа индексации. Он может быть относительным или абсолютным. Если он является относительным, то он добавляется к корневому каталогу проекта, который вы задаете в командной строке. Если он является абсолютным, то непосредственно используется в качестве корневого каталога этапа индексации. В нем допустимо использование прямой или обратной косой черты. Косые черты в конце строки не учитываются. Корневой каталог этапа индексации задает папку, с которой будут связанны все ресурсы.
- Атрибут `startIndexAt` — это файл или папка начального значения для индексации. Он связан с корневым каталогом этапа индексации. Пустое значение подразумевает использование корневой папки этапа индексации.

## <a name="default-pri-config-file"></a>Файл конфигурации PRI по умолчанию

MakePri.exe создает этот новый инициализированный файл конфигурации PRI, когда отдается [команда createconfig](makepri-exe-command-options.md#createconfig-command).

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<resources targetOsVersion="10.0.0" majorVersion="1">
  <packaging>
    <autoResourcePackage qualifier="Language"/>
    <autoResourcePackage qualifier="Scale"/>
    <autoResourcePackage qualifier="DXFeatureLevel"/>
  </packaging>
  <index root="\" startIndexAt="\">
    <default>
      <qualifier name="Language" value="en-US"/>
      <qualifier name="Contrast" value="standard"/>
      <qualifier name="Scale" value="100"/>
      <qualifier name="HomeRegion" value="001"/>
      <qualifier name="TargetSize" value="256"/>
      <qualifier name="LayoutDirection" value="LTR"/>
      <qualifier name="Theme" value="dark"/>
      <qualifier name="AlternateForm" value=""/>
      <qualifier name="DXFeatureLevel" value="DX9"/>
      <qualifier name="Configuration" value=""/>
      <qualifier name="DeviceFamily" value="Universal"/>
      <qualifier name="Custom" value=""/>
    </default>
    <indexer-config type="folder" foldernameAsQualifier="true" filenameAsQualifier="true" qualifierDelimiter="."/>
    <indexer-config type="resw" convertDotsToSlashes="true" initialPath=""/>
    <indexer-config type="resjson" initialPath=""/>
    <indexer-config type="PRI"/>
  </index>
  <!--<index startIndexAt="Start Index Here" root="Root Here">-->
  <!--        <indexer-config type="resfiles" qualifierDelimiter="."/>-->
  <!--        <indexer-config type="priinfo" emitStrings="true" emitPaths="true" emitEmbeddedData="true"/>-->
  <!--</index>-->
</resources>
```

## <a name="packaging-element"></a>Элемент packaging

Элемент `packaging` определяет раздельные сведения PRI. Схема для элемента `packaging` определяется и для автоматического (поддержка `autoResourcePackage` с заданным измерением), и для ручного режимов.

В этом примере показано, как использовать `autoResourcePackage` с заданным измерением.

```xml
    <packaging>
        <autoResourcePackage qualifier="Language"/>
        <autoResourcePackage qualifier="Scale"/>
        <autoResourcePackage qualifier="DXFeatureLevel"/>
    </packaging>
```

В этом примере показано, как использовать `resourcePackage` в ручном режиме.

```xml
  <packaging>
    <resourcePackage name="Germany">
      <qualifierSet definition="lang-de-de"/>
      <qualifierSet definition="lang-es-es"/>
    </resourcePackage>  
    <resourcePackage name="France">
      <qualifierSet definition="lang-fr-fr"/>
    </resourcePackage>  
    <resourcePackage name="HighRes1">
      <qualifierSet definition="scale-200"/>
    </resourcePackage>
    <resourcePackage name="HighRes2">
      <qualifierSet definition="scale-400"/>
    </resourcePackage>
  </packaging>
```

MakePri.exe явным образом не препятствует созданию PRI-файлов ресурсов с любым заданным измерением. Ограничения для конкретного набора измерений определяются и реализуются внешним образом при помощи MakeAppx.exe или других средств в конвейере.

MakePri.exe выполняет синтаксический анализ элемента `packaging` после всех узлов `index` для заполнения всех квалификаторов по умолчанию. MakePri.exe собирает проанализированную информацию в эти структуры данных.

```csharp
enum ResourcePackageMode
{
    None,
    AutoPackQualifier,
    ManualPack
}

ResourcePackageMode eResourcePackageMode;
list<string> RPQualifierList; // To store AutoResourcePackage Qualifiers
map<string, list<string>> RPNameToQSIMap; // To store ResourcePackage name to QualifierSet list mapping.
```

## <a name="resourcesisdeploymentmergeable-attribute"></a>Атрибут resources@isDeploymentMergeable

Этот атрибут задает флаг в файле PRI, что вызывает

- определение модулем развертывания, что этот PRI-файл можно объединить.
- GetFullyQualifiedReference возвращает ошибку, если этот флаг установлен и диспетчер ресурсов инициализирован с файлом.

Значение этого атрибута по умолчанию—`true`. MakePri.exe устанавливает флаг в PRI-файле, только если конфигурация предназначена для Windows10.

Рекомендуется опустить `isDeploymentMergeable` (либо присвоить явным образом значение `true`) для создания пакета ресурсов, если вы ориентируетесь на Windows 10.

MakePri.exe добавляет значение `isDeploymentMergeable` в файл дампа, если `makepri dump` выполняется с параметром `/dt detailed`.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<PriInfo>
    <PriHeader>
        ...
        <IsDeploymentMergeable>true</IsDeploymentMergeable>
        ...
    </PriHeader>
  ...
</PriInfo>
```

## <a name="resourcesmajorversion-attribute"></a>Атрибут resources@majorVersion

По умолчанию значение этого атрибута равно1. Если вы предоставляете явно заданное значение и также используете устаревший параметр командной строки `/VersionMajor(vma)` для средства MakePri.exe, то значение в файле конфигурации имеет приоритет.

Вот пример.

```xml
<resources majorVersion="2">
  <packaging ... />
  <index root="\" startIndexAt="\">
    ...
  </index>
</resources>
```

## <a name="resourcestargetosversion-attribute"></a>Атрибут resources@targetOsVersion

Указывает целевую версию операционной системы. В таблице ниже приведены поддерживаемые значения; значение по умолчанию — 6.3.0.

| Значение | Версия |
| ----- | ------- |
| 10.0.0 | Windows 10 |
| 6.3.0 (по умолчанию) | Windows8.1 |
| 6.2.1 | Windows 8 |

Вот пример.

```xml
<resources targetOsVersion="10.0.0">
  <packaging ... />
  <index root="\" startIndexAt="\">
    ...
  </index>
</resources>
```

**Примечание**. Windows обратно совместима относительно PRI файлов, но не всегда обеспечивается совместимость с последующими версиями.

MakePri.exe добавляет значение `targetOsVersion` в файл дампа, если `makepri dump` выполняется с параметром `/dt detailed`.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<PriInfo>
    <PriHeader>
        ...
        <TargetOS version="10.0.0"/>
        ...
    </PriHeader>
  ...
</PriInfo>
```

## <a name="validation-error-messages"></a>Сообщения об ошибках при проверке

Вот несколько примеров ошибок и соответствующего сообщения об ошибке.

| Ошибка | Серьезность | Сообщение |
| --------- | -------- | ------- |
| Указана targetOsVersion, отличная от одного из поддерживаемых значений. | Ошибка | Недопустимая конфигурация: указано недопустимое значение targetOsVersion. |
| Указана targetOsVersion «6.2.1» и элемент `packaging` присутствует. | Ошибка | Недопустимая конфигурация: узел упаковки не поддерживается с этим значением targetOsVersion. |
| В конфигурации обнаружены несколько режимов. Например, указаны ручной режим и AutoResourcePackage. | Ошибка | Недопустимая конфигурация: для узла упаковки не может быть нескольких рабочих режимов. |
| Для пакета ресурсов указан квалификатор по умолчанию. | Ошибка | Недопустимая конфигурация: <Qualifiername>=<QualifierValue> — это квалификатор по умолчанию, и его кандидаты нельзя добавить в пакет ресурсов. |
| Квалификатор AutoResourcePackage содержит несколько квалификаторов. Например, language_scale. | Ошибка | Недопустимая конфигурация: AutoResourcePackage с несколькими квалификаторами не поддерживается. |
| Набор квалификаторов ResourcePackage содержит несколько квалификаторов. Например, language-en-us_scale-100 | Ошибка | Недопустимая конфигурация: набор квалификаторов с несколькими квалификаторами не поддерживается. |
| Обнаружено повторяющееся имя пакета ресурсов. | Ошибка | Недопустимая конфигурация: повторяющееся имя пакета ресурсов <rpname>. |
| Один и тот же набор квалификаторов определен в двух пакетах ресурсов. | Ошибка | Недопустимая конфигурация: обнаружено несколько экземпляров набора квалификаторов <qualifier tags>. |
| Не найдено кандидатов для набора квалификаторов, указанного для узла ResourcePackage. | Предупреждение | Недопустимая конфигурация: кандидаты для <Resource Package Name> не найдены. |
| Не найдено кандидатов для квалификатора, указанного для узла AutoResourcePackage. | Предупреждение | Недопустимая конфигурация: кандидаты для квалификатора <qualifier name> не найдены. Пакет ресурсов не создан. |
| Не найдены режимы. То есть обнаружен пустой узел упаковки. | Предупреждение | Недопустимая конфигурация: не указан режим упаковки. |

## <a name="related-topics"></a>Статьи по теме

* [Компиляция ресурсов вручную с помощью MakePri.exe](compile-resources-manually-with-makepri.md)
* [Параметры командной строки MakePri.exe&mdash;команда createconfig](makepri-exe-command-options.md#createconfig-command)
* [Адаптация ресурсов с учетом языка, масштаба, высокой контрастности и других квалификаторов](tailor-resources-lang-scale-contrast.md)
* [Система управления ресурсами &mdash; ResourceContext](resource-management-system.md#resourcecontext)