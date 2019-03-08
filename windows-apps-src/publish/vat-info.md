---
Description: Если вам нужно ввести число, код НДС во время процесса регистрации центра партнеров, Вот некоторые сведения, чтобы приступить к работе.
title: Сведения об НДС
ms.assetid: 93834A6B-86D6-4647-AC01-CBCA3CB7A578
ms.date: 10/31/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 3d8b11815d7762179ad982edaf3985c06f9f54e4
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57648979"
---
# <a name="vat-info"></a>Сведения об НДС


Если вам нужно ввести число, код НДС во время процесса регистрации центра партнеров, Вот некоторые сведения, чтобы приступить к работе.

## <a name="understanding-vat-numbers"></a>Номера плательщиков НДС


Номер плательщика НДС — это идентификатор, используемый в странах и регионах ЕС. Дополнительные сведения см. на официальном сайте ЕС [VIES](https://go.microsoft.com/fwlink/p/?LinkId=258372).

## <a name="accepted-formats-for-vat-numbers"></a>Допустимые форматы номеров НДС


Обратите внимание, что Майкрософт не предоставляет налоговые консультации, и следующая таблица предоставляется только для справки. Обратитесь в местные налоговые органы по поводу последних изменений, если этих рекомендаций вам окажется недостаточно для того, чтобы предоставить Майкрософт номер плательщика НДС.

<table Responsive="true">
<tr><th>Страна или регион</th><th>Сведения об НДС</th></tr>
<tr><td data-th="Country/region">Австрия
</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: одна буква и 8 цифр</li>
<li>Код страны или региона: AT</li>
<li>Пример. U12345678</li>
<li>Примечания. Первый символ всегда будет «U».
</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Бельгия</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: десятичные цифры</li>
<li>Код страны или региона: BE</li>
<li>Пример. 1234567890</li>
<li>Примечания. 9 разрядов, до 1 января 2007 г.</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Болгария</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 9 или 10 цифр.</li>
<li>Код страны или региона: BG</li>
<li>Пример. 123456789 или 0123456789</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Хорватия</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: две буквы и цифры 11</li>
<li>Код страны или региона: Отдел кадров</li>
<li>Пример. HR12345678901</li>
<li>Примечания. Первые символы всегда будет «HR».</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Кипр</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: две буквы и цифры 8 и одна буква</li>
<li>Код страны или региона: CY</li>
<li>Пример. 12345678 123456789 и 0123456789</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Чешская республика</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 8, 9 или 10 цифр</li>
<li>Код страны или региона: CZ</li>
<li>Пример. 12345678 123456789 и 0123456789</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Дания</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 8 цифр</li>
<li>Код страны или региона: DK</li>
<li>Пример. 12345678</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Эстония</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 9 цифр</li>
<li>Код страны или региона: EE</li>
<li>Пример. 123456789</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Финляндия</td><td data-th="VAT info">
<ul>
<li>Формат номера: цифр</li>
<li>Код страны или региона: FI</li>
<li>Пример. 12345678</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Франция</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 11 цифр</li>
<li>Код страны или региона: FR</li>
<li>Пример. 12345678901, X1234567890, 1 x 123456789 или XX123456789</li>
<li>Примечания. Можно включить все буквенные символы, за исключением я или Q, как первый или второй символ, или как символ первой и второй, за которым следует 9 цифр.</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Германия</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 9 цифр</li>
<li>Код страны или региона: DE</li>
<li>Пример. 123456789</li>
<li>Примечания. Должно быть 9 цифра «Umsatzsteur Identifikationnummer» (Ust Nr идентификатор).</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Греция</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 9 цифр</li>
<li>Код страны или региона: EL-GR</li>
<li>Пример. 123456789</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Венгрия</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 8 цифр</li>
<li>Код страны или региона: HU</li>
<li>Пример. 12345678</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Ирландия</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 8 цифр</li>
<li>Код страны или региона: IE</li>
<li>Пример. 1234567 X или 1X34567X</li>
<li>Примечания. Включает 1 или 2 буквы: либо last, или второй и последний.</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Италия</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 11 цифр</li>
<li>Код страны или региона: ИТ-отдел</li>
<li>Пример. 12345678901</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Латвия</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 11 цифр</li>
<li>Код страны или региона: LV</li>
<li>Пример. 01234567890</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Литва</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 9 и 12 цифр</li>
<li>Код страны или региона: LT</li>
<li>Пример. 123456789 или 012345678901</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Люксембург</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 8 цифр</li>
<li>Код страны или региона: LU</li>
<li>Пример. 12345678</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Мальта</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: две буквы и цифры 8</li>
<li>Код страны или региона: MT</li>
<li>Пример. MT12345678</li>
<li>Примечания. Первые символы всегда будет «Главного целевого сервера».</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Нидерланды</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 11 цифр и одна буква</li>
<li>Код страны или региона: NL</li>
<li>Пример. 123456789B01</li>
<li>Примечания. Десятый знак всегда является «B».</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Польша</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: десятичные цифры</li>
<li>Код страны или региона: PL</li>
<li>Пример. 1234567890</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Португалия</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 9 цифр</li>
<li>Код страны или региона: PT</li>
<li>Пример. 123456789</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Румыния</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: две буквы и цифры 8-10</li>
<li>Код страны или региона: RO</li>
<li>Пример. RO12345678, RO123456789 или RO1234567890</li>
<li>Примечания. Первые символы всегда будет «RO».</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Словакия</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: десятичные цифры</li>
<li>Код страны или региона: SK</li>
<li>Пример. 1234567890</li>
<li>Примечания. Первые символы всегда будет «SI».</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Словения</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: две буквы и цифры 8</li>
<li>Код страны или региона: SI</li>
<li>Пример. SI12345678</li>
<li>Примечания. Первые символы всегда будет «SI».</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Испания</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 9 цифр</li>
<li>Код страны или региона: ES</li>
<li>Пример. X12345678 12345678 X и X1234567X</li>
<li>Примечания. Включает в себя буквы 1 или 2: первой, последней, или первый и последний.</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Швеция</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 12 цифр</li>
<li>Код страны или региона: SE</li>
<li>Пример. 123456789001</li>
<li>Примечания. Последние 2 символов должен быть "01".</li>
</ul>
</td></tr>
<tr><td data-th="Country/region">Соединенное Королевство</td><td data-th="VAT info">
<ul>
<li>Формат номера НДС: 9 и 12 цифр</li>
<li>Код страны или региона: ГБ</li>
<li>Пример. 123456789 или 123456789001</li>
<li>Примечания. 9 цифр в целом, но 12 цифр, если число представляет вложенные компании в пределах группы.</li>
</ul>
</td></tr>
</table>
                                                                                                                                                                  

 

 




