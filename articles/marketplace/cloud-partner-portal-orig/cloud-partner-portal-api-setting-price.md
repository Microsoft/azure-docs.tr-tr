---
title: Sanal makine teklifleri için fiyatlandırma | Azure Marketi
description: Sanal makine tekliflerinin Fiyatlandırmasını belirtmenin üç yöntemini açıklar.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: c7ea5afeb46c30837c2ae53e871bb64f5d8cf292
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827330"
---
<a name="pricing-for-virtual-machine-offers"></a>Sanal makine tekliflerinin fiyatlandırması
==================================

Sanal makine teklifleri için fiyatlandırma belirtmek için üç yol vardır: özelleştirilmiş çekirdek fiyatlandırma, çekirdek başına fiyatlandırma ve elektronik tablo fiyatlandırması.


<a name="customized-core-pricing"></a>Özelleştirilmiş çekirdek fiyatlandırma
-----------------------

Fiyatlandırma her bölge ve çekirdek birleşimine özgüdür. Satış listesindeki her bölge, tanımın **Virtualmachinefiyatlandırma**/**regionfiyatlar** bölümünde belirtilmelidir.  İsteğinizin her bir [bölgesi](#regions) için doğru para birimi kodlarını kullanın.  Aşağıdaki örnek bu gereksinimleri göstermektedir:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Çekirdek başına fiyatlandırma
----------------

Bu durumda, yayımcılar SKU 'ları için ABD Doları cinsinden bir fiyat belirtir ve diğer tüm fiyatlar otomatik olarak oluşturulur. İstekteki **tek** parametrede çekirdek başına fiyat belirtilir.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Elektronik tablo fiyatlandırması
-------------------

Yayımcı Ayrıca fiyatlandırma elektronik tablosunu geçici bir depolama konumuna yükleyebilir ve sonra diğer dosya yapıtları gibi isteğe URI 'yi dahil edebilir. Daha sonra bu elektronik tablo yüklenir, belirtilen fiyat zamanlamasını değerlendirmek için çevrilir ve son olarak teklifi fiyatlandırma bilgileriyle güncelleştirir. Teklifin sonraki GET istekleri, bölge için elektronik tablo URI 'sini ve değerlendirilen fiyatları döndürür.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="new-core-sizes-added-on-722019"></a>7/2/2019 üzerine yeni temel boyutlar eklendi
---------------------------

VM yayımcılarının, yeni Azure sanal makine boyutlarına (çekirdek sayısına göre) yeni fiyatlara ek olarak, 2 Temmuz 2019 tarihinde bilgilendirildi.  Yeni fiyatlar, 10, 44, 48, 60, 120, 208 ve 416 temel boyutlarına yöneliktir.  Mevcut VM için, bu çekirdek boyutları için yeni fiyatlar, geçerli fiyatlara göre otomatik olarak hesaplanır.  Yayımcılar, ek fiyatları gözden geçirmek ve istediğiniz değişiklikleri yapmak için 1 Ağustos 2019 ' e kadar.  Bu tarihten sonra, yayımcı tarafından zaten yeniden yayımlanmamışsa, bu yeni çekirdek boyutları için otomatik olarak hesaplanan fiyatlar geçerli olur.


<a name="regions"></a>Bölgeler
-------

Aşağıdaki tabloda, özelleştirilmiş çekirdek fiyatlandırma için belirtebileceğiniz farklı bölgeler ve bunlara karşılık gelen para birimi kodları gösterilmektedir.

| **Bölge** | **Ad**             | **Para birimi kodu** |
|------------|----------------------|-------------------|
| DZ         | Cezayir              | DZD               |
| AR         | Arjantin            | ARS               |
| AU         | Avustralya            | AUD               |
| AT         | Avusturya              | EUR               |
| BH         | Bahreyn              | BHD               |
| BY         | Belarus              | RUB               |
| BE         | Belçika              | EUR               |
| BR         | Brezilya               | USD               |
| BG         | Bulgaristan             | BGN               |
| CA         | Kanada               | CAD               |
| CL         | Şili                | CLP               |
| CO         | Kolombiya             | COP               |
| CR         | Kosta Rika           | CRC               |
| HR         | Hırvatistan              | HRK dili               |
| CY         | Kıbrıs               | EUR               |
| CZ         | Çek Cumhuriyeti       | CZK dili               |
| DK         | Danimarka              | DKK               |
| DO         | Dominik Cumhuriyeti   | USD               |
| EC         | Ekvador              | USD               |
| EG         | Mısır                | EGP               |
| SV         | El Salvador          | USD               |
| EE         | Estonya              | EUR               |
| FI         | Finlandiya              | EUR               |
| GS         | Fransa               | EUR               |
| DE         | Almanya              | EUR               |
| GR         | Yunanistan               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | Hong Kong ÖIB        | HKD               |
| HU         | Macaristan              | KUF               |
| IS         | İzlanda              | ISK               |
| IN         | Hindistan                | INR               |
| Kimlik         | Endonezya            | IDR               |
| IE         | İrlanda              | EUR               |
| IL         | İsrail               | ÖR               |
| BT         | İtalya                | EUR               |
| JP         | Japonya                | JPY               |
| JO         | Ürdün               | JOD               |
| KZ         | Kazakistan           | KZT               |
| KE         | Kenya                | KAR               |
| KR         | Güney Kore                | KRW               |
| KW         | Kuveyt               | KWD               |
| LV         | Letonya               | EUR               |
| LI         | Lihtenştayn        | CHF               |
| LT         | Litvanya            | EUR               |
| LU         | Lüksemburg           | EUR               |
| MK         | Kuzey Makedonya      | MKD               |
| MY         | Malezya             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Meksika               | MXN               |
| YAPA         | Karadağ           | EUR               |
| MA         | Fas              | MAD               |
| NL         | Hollanda          | EUR               |
| NZ         | Yeni Zelanda          | NZD               |
| NG         | Nijerya              | NGN               |
| NO         | Norveç               | NOK               |
| OM         | Umman                 | OMR               |
| PK         | Pakistan             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Peru                 | KADAR               |
| PH         | Filipinler          | PHP               |
| PL         | Polonya               | PLN               |
| PT         | Portekiz             | EUR               |
| PR         | Porto Riko          | USD               |
| QA         | Katar                | QAR               |
| RO         | Romanya              | IR               |
| RU         | Rusya               | RUB               |
| SA         | Suudi Arabistan         | SAR               |
| RS         | Sırbistan               | RSD               |
| SG         | Singapur            | SGD               |
| SK         | Slovakya             | EUR               |
| SI         | Slovenya             | EUR               |
| ZA         | Güney Afrika         | ZAR               |
| ES         | İspanya                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | İsveç               | SEK               |
| CH         | İsviçre          | CHF               |
| TW         | Tayvan               | TWD               |
| TH         | Tayland             | THB               |
| TT         | Trinidad ve Tobago  | TTD               |
| TN         | Tunus              | TND               |
| TR         | Türkiye               | TRY               |
| UA         | Ukrayna              | UAH               |
| AE         | Birleşik Arap Emirlikleri | EUR               |
| GB         | Birleşik Krallık       | GBP               |
| ABD         | Amerika Birleşik Devletleri        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
