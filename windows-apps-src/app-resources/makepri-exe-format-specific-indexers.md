---
author: stevewhims
Description: This topic describes the format-specific indexers used by the MakePri.exe tool to generate its index of resources.
title: Индексаторы для конкретных форматов MakePri.exe
template: detail.hbs
ms.author: stwhi
ms.date: 10/18/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, ресурс, изображение, средство, MRT, квалификатор
ms.localizationpriority: medium
ms.openlocfilehash: 8ec6b2a31f4f577de30dac1c96a411c6aee6e9dc
ms.sourcegitcommit: 194ab5aa395226580753869c6b66fce88be83522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "4148239"
---
# <a name="makepriexe-format-specific-indexers"></a>Индексаторы для конкретных форматов MakePri.exe

В этом разделе описываются специальные индексаторы, которые используются средством [MakePri.exe](compile-resources-manually-with-makepri.md) для создания указателя ресурсов.

> [!NOTE]
> MakePri.exe устанавливается во время проверки параметр **Windows SDK для управляемых приложений UWP** при установке пакета средств разработки программного обеспечения Windows. Он устанавливается на путь `%WindowsSdkDir%bin\<WindowsTargetPlatformVersion>\x64\makepri.exe` (а также в папки с именем для других архитектур). Например, `C:\Program Files (x86)\Windows Kits\10\bin\10.0.17713.0\x64\makepri.exe`.

MakePri.exe обычно используется вместе с командами `new`, `versioned` и `resourcepack`. См. [Параметры командной строки MakePRI.exe](makepri-exe-command-options.md). В таких случаях программа индексирует исходные файлы, чтобы создать индекс ресурсов. MakePri.exe использует различные отдельные индексаторы для чтения различных исходных файлов ресурсов или контейнеров для ресурсов. Самый простой индексатор — это индексатор папки, который индексирует содержимое папки, например изображения в форматах `.jpg` или `.png`.

Индексаторы для конкретных форматов отличаются наличием элементов `<indexer-config>` в элементе `<index>` [файла конфигурации MakePri.exe](makepri-exe-configuration.md). Используемый индексатор для конкретных форматов определяется атрибутом `type`.

Если в ходе индексации встречаются контейнеры ресурсов, то обычно индексируется их содержимое, но сами они в индекс не добавляются. Например, файлы `.resjson`, найденные индексатором папки, могут быть далее обработаны индексатором для формата `.resjson`, и в этом случае сам файл `.resjson` в индексе не появится. **Примечание**. Для этого элемент `<indexer-config>` для индексатора, связанного с данным контейнером, необходимо включить в файл конфигурации.

Обычно квалификаторы, указанные в контейнере &mdash; например в папке или файле `.resw` &mdash; применяются ко всем ресурсам внутри контейнера, например к файлам внутри папки или к строкам в файле `.resw`.

## <a name="folder"></a>Папка

Индексатор папки задается атрибутом `type` со значением FOLDER. Индексирует содержимое папки и определяет квалификаторы ресурсов из папки и имен файлов. Элемент конфигурации соответствует следующей схеме.

```xml
<xs:schema attributeFormDefault=\"unqualified\" elementFormDefault=\"qualified\" xmlns:xs=\"http://www.w3.org/2001/XMLSchema\">\
    <xs:simpleType name=\"ExclusionTypeList\">\
        <xs:restriction base=\"xs:string\">\
            <xs:enumeration value=\"path\"/>\
            <xs:enumeration value=\"extension\"/>\
            <xs:enumeration value=\"name\"/>\
            <xs:enumeration value=\"tree\"/>\
        </xs:restriction>\
    </xs:simpleType>\
    <xs:complexType name=\"FolderExclusionType\">\
        <xs:attribute name=\"type\" type=\"ExclusionTypeList\" use=\"required\"/>\
        <xs:attribute name=\"value\" type=\"xs:string\" use=\"required\"/>\
        <xs:attribute name=\"doNotTraverse\" type=\"xs:boolean\" use=\"required\"/>\
        <xs:attribute name=\"doNotIndex\" type=\"xs:boolean\" use=\"required\"/>\
    </xs:complexType>\
    <xs:simpleType name=\"IndexerConfigFolderType\">\
        <xs:restriction base=\"xs:string\">\
            <xs:pattern value=\"((f|F)(o|O)(l|L)(d|D)(e|E)(r|R))\"/>\
        </xs:restriction>\
    </xs:simpleType>\
    <xs:element name=\"indexer-config\">\
        <xs:complexType>\
            <xs:sequence>\
                <xs:element name=\"exclude\" type=\"FolderExclusionType\" minOccurs=\"0\" maxOccurs=\"unbounded\"/>\
            </xs:sequence>\
            <xs:attribute name=\"type\" type=\"IndexerConfigFolderType\" use=\"required\"/>\
            <xs:attribute name=\"foldernameAsQualifier\" type=\"xs:boolean\" use=\"required\"/>\
            <xs:attribute name=\"filenameAsQualifier\" type=\"xs:boolean\" use=\"required\"/>\
            <xs:attribute name=\"qualifierDelimiter\" type=\"xs:string\" use=\"required\"/>\
        </xs:complexType>\
    </xs:element>\
</xs:schema>
```

Атрибут `qualifierDelimiter` указывает символ, после которого в имени файла указываются квалификаторы. При этом расширение игнорируется. Значение по умолчанию — ".".

## <a name="pri"></a>PRI

Индексатор PRI задается атрибутом `type` со значением PRI. Он индексирует содержимое PRI-файла. Обычно используется при индексации ресурса, который содержится в другой сборке, библиотеке DLL, пакете SDK или библиотеке классов в PRI-файле приложения.

Все имена ресурсов, квалификаторы и значения, которые входят в состав PRI-файла, напрямую сохраняются в новом PRI-файле. Однако карта ресурсов верхнего уровня не содержится в конечном PRI-файле. Карты ресурсов объединяются.

```xml
<xs:schema id=\"prifile\" xmlns:xs=\"http://www.w3.org/2001/XMLSchema\" elementFormDefault=\"qualified\">\
    <xs:simpleType name=\"IndexerConfigPriType\">\
        <xs:restriction base=\"xs:string\">\
            <xs:pattern value=\"((p|P)(r|R)(i|I))\"/>\
        </xs:restriction>\
    </xs:simpleType>\
    <xs:element name=\"indexer-config\">\
        <xs:complexType>\
            <xs:attribute name=\"type\" type=\"IndexerConfigPriType\" use=\"required\"/>\
        </xs:complexType>\
    </xs:element>\
</xs:schema>\
```

## <a name="priinfo"></a>PriInfo

Индексатор PriInfo задается атрибутом `type` со значением PRIINFO. Он индексирует содержимое подробного файла дампа. Создать подробный файл дампа можно, выполнив `makepri dump` с параметром `/dt detailed`. Элемент конфигурации индексатора соответствует следующей схеме.

```xml
<xs:schema id="priinfo" xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified">
  <xs:simpleType name="IndexerConfigPriInfoType">
    <xs:restriction base="xs:string">
      <xs:pattern value="((p|P)(r|R)(i|I)(i|I)(n|N)(f|F)(o|O))"/>
    </xs:restriction>
  </xs:simpleType>
  <xs:element name="indexer-config">
    <xs:complexType>
      <xs:attribute name="type" type="IndexerConfigPriInfoType" use="required"/>
      <xs:attribute name="emitStrings" type="xs:boolean" use="optional"/>
      <xs:attribute name="emitPaths" type="xs:boolean" use="optional"/>
    </xs:complexType>
  </xs:element>
</xs:schema>
```

Этот элемент конфигурации разрешает использование дополнительных параметров для настройки реакции индексатора PRIInfo на события. Значением по умолчанию для `emitStrings` и `emitPaths` является `true`. Если `emitStrings` — `true`, то ресурсы-кандидаты с атрибутом `type`, равным String, будут включены в индекс, в противном случае они будут исключены. Если emitPaths — `true`, то ресурсы-кандидаты с атрибутом `type`, равным Path, будут включены в индекс, в противном случае они будут исключены.

Ниже показан пример конфигурации, которая включает типы ресурсов String и не включает типы ресурсов Path:

```xml
<indexer-config type="priinfo" emitStrings="true" emitPaths="false" />
```

Для индексации файл дампа должен иметь расширение `.pri.xml` и соответствовать следующей схеме.

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified" >\
  <xs:simpleType name="candidateType">\
    <xs:restriction base="xs:string">\
      <xs:pattern value="Path|String"/>\
    </xs:restriction>\
  </xs:simpleType>\
  <xs:complexType name="scopeType">\
    <xs:sequence>\
      <xs:element name="ResourceMapSubtree" type="scopeType" minOccurs="0" maxOccurs="unbounded"/>\
      <xs:element name="NamedResource" minOccurs="0" maxOccurs="unbounded">\
        <xs:complexType>\
          <xs:sequence>\
            <xs:element name="Decision" minOccurs="0" maxOccurs="unbounded">\
              <xs:complexType>\
                <xs:sequence>\
                  <xs:any processContents="skip" minOccurs="0" maxOccurs="unbounded"/>\
                </xs:sequence>\
                <xs:anyAttribute processContents="skip" />\
              </xs:complexType>\
            </xs:element>\
            <xs:element name="Candidate" minOccurs="0" maxOccurs="unbounded">\
              <xs:complexType>\
                <xs:sequence>\
                  <xs:element name="QualifierSet" minOccurs="0" maxOccurs="unbounded">\
                    <xs:complexType>\
                      <xs:sequence>\
                        <xs:element name="Qualifier" minOccurs="0" maxOccurs="unbounded">\
                          <xs:complexType>\
                            <xs:attribute name="name" type="xs:string" use="required" />\
                            <xs:attribute name="value" type="xs:string" use="required" />\
                            <xs:attribute name="priority" type="xs:integer" use="required" />\
                            <xs:attribute name="scoreAsDefault" type="xs:decimal" use="required" />\
                            <xs:attribute name="index" type="xs:integer" use="required" />\
                          </xs:complexType>\
                        </xs:element>\
                      </xs:sequence>\
                      <xs:anyAttribute processContents="skip" />\
                    </xs:complexType>\
                  </xs:element>\
                  <xs:element name="Value" type="xs:string"  minOccurs="0" maxOccurs="unbounded"/>\
                </xs:sequence>\
                <xs:attribute name="type" type="candidateType" use="required" />\
              </xs:complexType>\
            </xs:element>\
          </xs:sequence>\
          <xs:attribute name="name" use="required" type="xs:string" />\
          <xs:anyAttribute processContents="skip" />\
        </xs:complexType>\
      </xs:element>\
    </xs:sequence>\
    <xs:attribute name="name" use="required" type="xs:string" />\
    <xs:anyAttribute processContents="skip" />\
  </xs:complexType>\
  <xs:element name="PriInfo">\
    <xs:complexType>\
      <xs:sequence>\
        <xs:element name="PriHeader" >\
          <xs:complexType>\
            <xs:sequence>\
              <xs:any minOccurs ="0" maxOccurs="unbounded" processContents="skip" />\
            </xs:sequence>\
            <xs:anyAttribute processContents="skip" />\
          </xs:complexType>\
        </xs:element>\
        <xs:element name="QualifierInfo">\
          <xs:complexType>\
            <xs:sequence>\
              <xs:any minOccurs="0" maxOccurs="unbounded" processContents="skip" />\
            </xs:sequence>\
          </xs:complexType>\
        </xs:element>\
        <xs:element name="ResourceMap">\
          <xs:complexType>\
            <xs:sequence>\
              <xs:element name="VersionInfo">\
                <xs:complexType>\
                  <xs:anyAttribute processContents="skip" />\
                </xs:complexType>\
              </xs:element>\
              <xs:element minOccurs="0" maxOccurs="unbounded" name="ResourceMapSubtree" type="scopeType" />\
            </xs:sequence>\
            <xs:attribute name="name" type="xs:string" use="required" />\
            <xs:anyAttribute processContents="skip" />\
          </xs:complexType>\
        </xs:element>\
      </xs:sequence>\
    </xs:complexType>\
  </xs:element>\
</xs:schema>
```

MakePri.exe поддерживает следующие типы дампа: основной, подробный, схема и сводка. Чтобы настроить MakePri.exe на создание типа дампа, который сможет прочитать индексатор PRIInfo, добавьте при задании команды `dump` параметр /DumpType Detailed.

MakePri.exe пропускает некоторые элементы файла `.pri.xml`. Эти элементы вычисляются либо во время индексации, либо указываются в файле конфигурации MakePri.exe. Имена ресурсов, квалификаторы и значения, которые содержатся в файле дампа, непосредственно содержатся в новом PRI-файле. Карта ресурсов верхнего уровня не содержится в конечном PRI-файле. Во время индексации карты ресурсов объединяются.

Пример допустимого ресурса строкового типа из файла дампа.

```xml
<NamedResource name="SampleString " index="96" uri="ms-resource://SampleApp/resources/SampleString ">
  <Decision index="2">
    <QualifierSet index="1">
      <Qualifier name="Language" value="EN-US" priority="900" scoreAsDefault="1.0" index="1"/>
    </QualifierSet>
  </Decision>
  <Candidate type="String">
    <QualifierSet index="1">
      <Qualifier name="Language" value="EN-US" priority="900" scoreAsDefault="1.0" index="1"/>
    </QualifierSet>
    <Value>A Sample String Value</Value>
  </Candidate>
</NamedResource>
```

Пример допустимого ресурса типа Path с двумя кандидатами из файла дампа.

```xml
<NamedResource name="Sample.png" index="77" uri="ms-resource://SampleApp/Files/Images/Sample.png">
  <Decision index="2">
    <QualifierSet index="1">
      <Qualifier name="Scale" value="180" priority="500" scoreAsDefault="1.0" index="1"/>
    </QualifierSet>
    <QualifierSet index="2">
      <Qualifier name="Scale" value="140" priority="500" scoreAsDefault="0.7" index="2"/>
    </QualifierSet>
  </Decision>
  <Candidate type="Path">
    <QualifierSet index="1">
      <Qualifier name="Scale" value="180" priority="500" scoreAsDefault="1.0" index="1"/>
    </QualifierSet>
    <Value>Images\Sample.scale-180.png</Value>
  </Candidate>
  <Candidate type="Path">
    <QualifierSet index="2">
      <Qualifier name="Scale" value="140" priority="500" scoreAsDefault="1.0" index="1"/>
    </QualifierSet>
    <Value>Images\Sample.scale-140.png</Value>
  </Candidate>
</NamedResource>
```

## <a name="resfiles"></a>ResFiles

Индексатор ResFiles задается атрибутом `type` со значением RESFILES. Он индексирует содержимое файла `.resfiles`. Элемент конфигурации соответствует следующей схеме.

```xml
<xs:schema id=\"resx\" xmlns:xs=\"http://www.w3.org/2001/XMLSchema\" elementFormDefault=\"qualified\">\
    <xs:simpleType name=\"IndexerConfigResFilesType\">\
        <xs:restriction base=\"xs:string\">\
            <xs:pattern value=\"((r|R)(e|E)(s|S)(f|F)(i|I)(l|L)(e|E)(s|S))\"/>\
        </xs:restriction>\
    </xs:simpleType>\
    <xs:element name=\"indexer-config\">\
        <xs:complexType>\
            <xs:attribute name=\"type\" type=\"IndexerConfigResFilesType\" use=\"required\"/>\
            <xs:attribute name=\"qualifierDelimiter\" type=\"xs:string\" use=\"required\"/>\
        </xs:complexType>\
    </xs:element>\
</xs:schema>\
```

Файл `.resfiles` — это текстовый файл, который содержит плоский список путей к файлу. Файл `.resfiles` может содержать комментарии вида "//". Вот пример.

```
Strings\component1\fr\elements.resjson
Images\logo.scale-100.png
Images\logo.scale-140.png
Images\logo.scale-180.png
```

## <a name="resjson"></a>ResJSON

Индексатор ResJSON задается атрибутом `type` со значением RESJSON. Он индексирует содержимое файла `.resjson`, который является строковым файлом ресурсов. Элемент конфигурации соответствует следующей схеме.

```xml
<xs:schema id=\"resjson\" xmlns:xs=\"http://www.w3.org/2001/XMLSchema\" elementFormDefault=\"qualified\">\
    <xs:simpleType name=\"IndexerConfigResJsonType\">\
        <xs:restriction base=\"xs:string\">\
            <xs:pattern value=\"((r|R)(e|E)(s|S)(j|J)(s|S)(o|O)(n|N))\"/>\
        </xs:restriction>\
    </xs:simpleType>\
    <xs:element name=\"indexer-config\">\
        <xs:complexType>\
            <xs:attribute name=\"type\" type=\"IndexerConfigResJsonType\" use=\"required\"/>\
            <xs:attribute name=\"initialPath\" type=\"xs:string\" use=\"optional\"/>\
        </xs:complexType>\
    </xs:element>\
</xs:schema>\
```

Файл `.resjson` содержит текст JSON, как определено в стандарте [The application/json Media Type for JavaScript Object Notation (JSON)](http://www.ietf.org/rfc/rfc4627.txt). В файле должен содержаться один объект JSON с иерархическими свойствами. Каждое свойство должно быть либо другим объектом JSON, либо строковым значением.

Свойства JSON, чьи имена начинаются с символа подчеркивания ("_"), не компилируются в конечный PRI-файл, но содержатся в файле журнала.

Файл также может содержать комментарии вида "//", которые игнорируются при синтаксическом анализе.

Атрибут `initialPath` помещает все ресурсы по исходному пути, который задается в начале имени ресурса. Обычно этот индексатор применяется при индексации ресурсов библиотек классов. Значение по умолчанию — blank.

## <a name="resw"></a>ResW

Индексатор ResW задается атрибутом `type` со значением RESW. Он индексирует содержимое файла `.resw`, который является строковым файлом ресурсов. Элемент конфигурации соответствует следующей схеме.

```xml
<xs:schema id=\"resw\" xmlns:xs=\"http://www.w3.org/2001/XMLSchema\" elementFormDefault=\"qualified\">\
    <xs:simpleType name=\"IndexerConfigResxType\">\
        <xs:restriction base=\"xs:string\">\
            <xs:pattern value=\"((r|R)(e|E)(s|S)(w|W))\"/>\
        </xs:restriction>\
    </xs:simpleType>\
    <xs:element name=\"indexer-config\">\
        <xs:complexType>\
            <xs:attribute name=\"type\" type=\"IndexerConfigResxType\" use=\"required\"/>\
            <xs:attribute name=\"convertDotsToSlashes\" type=\"xs:boolean\" use=\"required\"/>\
            <xs:attribute name=\"initialPath\" type=\"xs:string\" use=\"optional\"/>\
        </xs:complexType>\
    </xs:element>\
</xs:schema>\
```

Файл `.resw` — это XML-файл, который соответствует следующей схеме.

```xml
  <xsd:schema id="root" xmlns="" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata">
    <xsd:import namespace="http://www.w3.org/XML/1998/namespace" />
    <xsd:element name="root" msdata:IsDataSet="true">
      <xsd:complexType>
        <xsd:choice maxOccurs="unbounded">
          <xsd:element name="metadata">
            <xsd:complexType>
              <xsd:sequence>
                <xsd:element name="value" type="xsd:string" minOccurs="0" />
              </xsd:sequence>
              <xsd:attribute name="name" use="required" type="xsd:string" />
              <xsd:attribute name="type" type="xsd:string" />
              <xsd:attribute name="mimetype" type="xsd:string" />
              <xsd:attribute ref="xml:space" />
            </xsd:complexType>
          </xsd:element>
          <xsd:element name="assembly">
            <xsd:complexType>
              <xsd:attribute name="alias" type="xsd:string" />
              <xsd:attribute name="name" type="xsd:string" />
            </xsd:complexType>
          </xsd:element>
          <xsd:element name="data">
            <xsd:complexType>
              <xsd:sequence>
                <xsd:element name="value" type="xsd:string" minOccurs="0" msdata:Ordinal="1" />
                <xsd:element name="comment" type="xsd:string" minOccurs="0" msdata:Ordinal="2" />
              </xsd:sequence>
              <xsd:attribute name="name" type="xsd:string" use="required" msdata:Ordinal="1" />
              <xsd:attribute name="type" type="xsd:string" msdata:Ordinal="3" />
              <xsd:attribute name="mimetype" type="xsd:string" msdata:Ordinal="4" />
              <xsd:attribute ref="xml:space" />
            </xsd:complexType>
          </xsd:element>
          <xsd:element name="resheader">
            <xsd:complexType>
              <xsd:sequence>
                <xsd:element name="value" type="xsd:string" minOccurs="0" msdata:Ordinal="1" />
              </xsd:sequence>
              <xsd:attribute name="name" type="xsd:string" use="required" />
            </xsd:complexType>
          </xsd:element>
        </xsd:choice>
      </xsd:complexType>
    </xsd:element>
  </xsd:schema>
```

Атрибут `convertDotsToSlashes` заменяет все точки (".") в именах ресурсов (атрибуты имени элемента данных) на косую черту "/", кроме случаев, когда точки расположены между символами "[" и "]".

Атрибут `initialPath` помещает все ресурсы по исходному пути, который задается в начале имени ресурса. Обычно этот индексатор применяется при индексации ресурсов библиотек классов. Значение по умолчанию — blank.

## <a name="related-topics"></a>Статьи по теме

* [Компиляция ресурсов вручную с помощью MakePri.exe](compile-resources-manually-with-makepri.md)
* [Параметры командной строки MakePRI.exe](makepri-exe-command-options.md)
* [Файл конфигурации MakePri.exe](makepri-exe-configuration.md)
* [Стандарт The application/json Media Type for JavaScript Object Notation (JSON)](http://www.ietf.org/rfc/rfc4627.txt)