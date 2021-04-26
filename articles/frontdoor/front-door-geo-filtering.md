---
title: Azure ön kapısı için bir etki alanında coğrafi filtreleme | Microsoft Docs
description: Bu makalede, Azure ön kapısının coğrafi filtreleme ilkesi hakkında bilgi edinirsiniz
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2020
ms.author: duau
ms.reviewer: tyao
ms.openlocfilehash: 33ad17e3024011368c909a89e9164ca06d044bad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612091"
---
# <a name="geo-filtering-on-a-domain-for-azure-front-door"></a>Azure ön kapısı için bir etki alanında coğrafi filtreleme

Varsayılan olarak, Azure ön kapısı, isteğin geldiği konumdan bağımsız olarak tüm Kullanıcı isteklerine yanıt verir. Bazı senaryolarda, Web uygulamanıza olan erişimi ülkelere/bölgelere göre kısıtlamak isteyebilirsiniz. Ön kapıdaki Web uygulaması güvenlik duvarı (WAF) hizmeti, belirli ülkelerde/bölgelerde erişime izin vermek veya erişimi engellemek için uç noktanıza özel erişim kuralları kullanarak bir ilke tanımlamanızı sağlar. 

Bir WAF ilkesi özel kurallar kümesi içerir. Kural eşleşme koşullarından, bir eylemden ve bir öncelikte oluşur. Match koşulunda bir Match değişkeni, işleç ve Match değeri tanımlarsınız. Bir coğrafi filtreleme kuralı için, bir eşleşme değişkeni REMOTE_ADDR, işleç coğrafi eşleşmedir ve değer ilgilendiğiniz iki harfli ülke/bölge kodu olur. "ZZ" ülke kodu veya "Bilinmeyen" ülke, veri kümizdeki bir ülkeye henüz eşlenmemiş IP adreslerini yakalar. Hatalı pozitif sonuçlar oluşmasını önlemek için, eşleştirme koşullarınıza ZZ ekleyebilirsiniz. Yol tabanlı bir coğrafi filtreleme kuralı oluşturmak için bir GeoMatch koşulunu ve bir REQUEST_URI dize eşleşme koşulunu birleştirebilirsiniz. 


[Azure PowerShell](front-door-tutorial-geo-filtering.md) kullanarak veya bir [hızlı başlangıç şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)kullanarak, ön kapıya yönelik bir coğrafi filtreleme ilkesi yapılandırabilirsiniz.

## <a name="countryregion-code-reference"></a>Ülke/bölge kodu başvurusu

|Ülke/bölge kodu | Ülke/bölge adı |
| ----- | ----- |
| AD | Andorra |
| AE | Birleşik Arap Emirlikleri|
| AF | Afganistan|
| AG | Antigua ve Barbuda|
| AL | Arnavutluk|
| AM | Ermenistan|
| AO | Angola|
| AR | Arjantin|
| AS | Amerikan Samoası|
| AT | Avusturya|
| AU | Avustralya|
| AZ | Azerbaycan|
| BA | Bosna-Hersek|
| BB | Barbados|
| BD | Bangladeş|
| BE | Belçika|
| BF | Burkina Faso|
| BG | Bulgaristan|
| BH | Bahreyn|
| BI | Burundi|
| BJ | Benin|
| BL | Saint Barthelemy|
| BN | Barış Yurdu Brunei Devleti|
| BO | Bolivya|
| BR | Brezilya|
| BS | Bahamalar|
| BT | Butan|
| BW | Botsvana|
| BY | Belarus|
| BZ | Belize|
| CA | Kanada|
| CD | Demokratik Kongo Cumhuriyeti|
| CF | Orta Afrika Cumhuriyeti|
| CH | İsviçre|
| CI | Fildişi Kıyısı|
| CL | Şili|
| CM | Kamerun|
| CN | Çin|
| CO | Kolombiya|
| CR | Kosta Rika|
| CU | Küba|
| CV | Cabo Verde|
| CY | Kıbrıs|
| CZ | Çek Cumhuriyeti|
| DE | Almanya|
| DK | Danimarka|
| DO | Dominik Cumhuriyeti|
| DZ | Cezayir|
| EC | Ekvador|
| EE | Estonya|
| EG | Mısır|
| ES | İspanya|
| ET | Etiyopya|
| FI | Finlandiya|
| FJ | Fiji|
| FM | Mikronezya Federe Devletleri|
| GS | Fransa|
| GB | Birleşik Krallık|
| GE | Gürcistan|
| GF | Fransız Guyanası|
| GH | Gana|
| GN | Gine|
| GP | Guadeloupe|
| GR | Yunanistan|
| GT | Guatemala|
| GY | Guyana|
| HK | Hong Kong ÖİB|
| HN | Honduras|
| HR | Hırvatistan|
| HT | Haiti|
| HU | Macaristan|
| ID | Endonezya|
| IE | İrlanda|
| IL | İsrail|
| IN | Hindistan|
| IQ | Irak|
| IR | İran İslam Cumhuriyeti|
| IS | İzlanda|
| BT | İtalya|
| JM | Jamaika|
| JO | Ürdün|
| JP | Japonya|
| KE | Kenya|
| KG | Kırgızistan|
| KH | Kamboçya|
| KI | Kiribati|
| KN | Saint Kitts ve Nevis|
| KP | Kore Demokratik Halk Cumhuriyeti|
| KR | Kore Cumhuriyeti|
| KW | Kuveyt|
| KY | Cayman Adaları|
| KZ | Kazakistan|
| LA | Laos Demokratik Halk Cumhuriyeti|
| LB | Lübnan|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberya|
| LS | Lesotho|
| LT | Litvanya|
| LU | Lüksemburg|
| LV | Letonya|
| LY | Libya |
| MA | Fas|
| MD | Moldova Cumhuriyeti|
| MG | Madagaskar|
| MK | Kuzey Makedonya|
| ML | Mali|
| MM | Myanmar|
| MN | Moğolistan|
| MO | Makao ÖİB|
| MQ | Martinique|
| MR | Moritanya|
| MT | Malta|
| MV | Maldivler|
| MW | Malavi|
| MX | Meksika|
| MY | Malezya|
| MZ | Mozambik|
| NA | Namibya|
| NE | Nijer|
| NG | Nijerya|
| NI | Nikaragua|
| NL | Hollanda|
| NO | Norveç|
| NP | Nepal|
| NR | Nauru|
| NZ | Yeni Zelanda|
| OM | Umman|
| PA | Panama|
| PE | Peru|
| PH | Filipinler|
| PK | Pakistan|
| PL | Polonya|
| PR | Porto Riko|
| PT | Portekiz|
| PW | Palau|
| PY | Paraguay|
| QA | Katar|
| RE | Reunion|
| RO | Romanya|
| RS | Sırbistan|
| RU | Rusya Federasyonu|
| RW | Ruanda|
| SA | Suudi Arabistan|
| SD | Sudan|
| SE | İsveç|
| SG | Singapur|
| SI | Slovenya|
| SK | Slovakya|
| SN | Senegal|
| SO | Somali|
| SR | Surinam|
| SS | Güney Sedan|
| SV | El Salvador|
| SY | Suriye Arap Cumhuriyeti|
| SZ | Svaziland|
| TC | Turks ve Caicos Adaları|
| TG | Togo|
| TH | Tayland|
| TN | Tunus|
| TR | Türkiye|
| TT | Trinidad ve Tobago|
| TW | Tayvan|
| TZ | Tanzanya Birleşik Cumhuriyeti|
| UA | Ukrayna|
| UG | Uganda|
| ABD | Birleşik Devletler|
| UY | Uruguay|
| UZ | Özbekistan|
| VC | Saint Vincent ve Grenadinler|
| VE | Venezuela|
| VG | Virgin Adaları, İngiliz|
| VI | Virgin Adaları, ABD|
| VN | Vietnam|
| ZA | Güney Afrika|
| ZM | Zambiya|
| ZW | Zimbabve|

## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Coğrafi filtreleme WAF ilkesi ayarlamayı](front-door-tutorial-geo-filtering.md)öğrenin.
