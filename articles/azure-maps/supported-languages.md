---
title: Azure haritalar 'da yerelleştirme desteği | Microsoft Docs
description: Azure haritalar 'daki hizmetler için desteklenen diller hakkında bilgi edinin
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3382cffdc41685f8329a640aaf6c6c526375a83
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299831"
---
# <a name="localization-support-in-azure-maps"></a>Azure haritalar 'da yerelleştirme desteği

Azure haritalar, ülkeye/bölgeye göre çeşitli dilleri ve görünümleri destekler. Bu makalede, Azure haritalar uygulamanıza kılavuzluk etmenize yardımcı olacak desteklenen diller ve görünümler sağlanmaktadır.


## <a name="azure-maps-supported-languages"></a>Azure haritalar desteklenen diller

Azure haritalar, hizmetler genelinde çeşitli dillerde yerelleştirilmiştir. Aşağıdaki tabloda her hizmet için desteklenen dil kodları sağlanmaktadır.  
  

| Kimlik         | Adı                   |  Haritalar | Arama | Yönlendirme | Trafik olayları | JS eşleme denetimi | Saat dilimi |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| AF-ZA      | Afrikaner              |       |    ✓   |    ✓    |                   |                |     ✓     |
| ar-SA      | Arapça                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| AB-ES      | Bask dili                 |       |    ✓   |         |                   |                |     ✓     |
| BG-BG      | Bulgarca              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| CA-ES      | Katalanca                |       |    ✓   |         |                   |                |     ✓     |
| zh-HanS    | Çince (Basitleştirilmiş)   |       |  zh-CN |         |                   |                |     ✓     |
| zh-HanT    | Çince (Geleneksel)  | zh-TW |  zh-TW |  zh-TW  |                   |      zh-TW     |     ✓     |
| HR-SA      | Hırvatça               |       |    ✓   |         |                   |                |     ✓     |
| CS-CZ      | Çekçe                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| da-DK      | Danca                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NL-NL      | Hollanda dili                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-      | Felemenkçe (Belçika)        |       |    ✓   |         |                   |                |     ✓     |
| En-AU      | İngilizce (Avustralya)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| En-NZ      | İngilizce (Yeni Zelanda)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-GB      | İngilizce (Büyük Britanya) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-US      | İngilizce (ABD)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| et-EE      | Estonca               |       |    ✓   |         |         ✓         |                |     ✓     |
| Fi-FI      | Fince                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-FR      | Fransızca                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-CA      | Fransızca (Kanada)      |       |    ✓   |         |                   |                |     ✓     |
| GL-ES      | Galiçya lehçesi               |       |    ✓   |         |                   |                |     ✓     |
| de-DE      | Almanca                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| El-GR      | Yunanca                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| BT-Il      | İbranice                 |       |    ✓   |         |         ✓         |                |     ✓     |
| Merhaba      | Hintçe                  |       |        |         |                   |                |     ✓     |
| HU-HU      | Macarca              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| kimlik KIMLIĞI      | Endonezya dili             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| BT BT      | İtalyanca                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | Japonca               |       |        |         |                   |                |     ✓     |
| kk-KZ      | Kazakça                 |       |    ✓   |         |                   |                |     ✓     |
| ko-KR      | Korece                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| es-419     | Latin Amerika Ispanyolca |       |    ✓   |         |                   |                |     ✓     |
| LV-LV      | Letonca                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt-LT      | Litvanca             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| MS-MY      | Malay dili (Latin)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| NB-hayır      | Norveççe Bokmål       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Yerel betiklerdeki tüm bölgeler için nötr on-resmi diller varsa |   ✓     |        |         |                   |      ✓          |         |
| NGT-Latn   | Nötr zemin.-Latin exonbiri. Varsa Latin betiği kullanılacaktır |   ✓     |        |         |                   |        ✓         |          |
| PL-PL      | Lehçe                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| PT-BR      | Portekizce (Brezilya)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| PT NK      | Portekizce (Portekiz)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| RO-RO      | Rumence               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru-RU      | Rusça                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| SR-Cyrl-RS | Sırpça (Kiril)     |       |    Sırpça (Kiril) (SR-RS)   |         |                   |                |     ✓     |
| sr-Latn-RS | Sırpça (Latin)        |       |        |         |                   |                |     ✓     |
| SK-SK      | Slovakça              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| SL-SL      | Slovence              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| ES-ES      | İspanyolca                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| es-MX      | İspanyolca (Meksika)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| ZF-s     | İsveççe                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| TH-TH      | Tay dili                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| tr-TR      | Türkçe                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| UK-UA      | Ukrayna dili               |       |    ✓   |         |                   |                |     ✓     |
| vi-VN      | Vietnam dili             |       |    ✓   |         |                   |                |     ✓     |


## <a name="azure-maps-supported-views"></a>Azure haritalar desteklenen görünümler

> [!Note]
> Azure haritalar, 1 Ağustos 2019 ' de aşağıdaki ülkelerde/bölgelerde yayımlanmıştır:
>  * Arjantin
>  * Hindistan
>  * Fas
>  * Pakistan
>
> 1 Ağustos 2019 ' den sonra, **görüntüleme** parametresi ayarı yukarıda listelenen yeni bölgeler/ülkeler için döndürülen harita içeriğini tanımlar. Görüntüleme parametresini, hizmetlerinizin kullandığı REST API 'Ler ve SDK 'Lar için gereken şekilde ayarlamış olduğunuzdan emin olmanızı öneririz.
>  
>
>  **REST API 'Leri:**
>  
>  Görünüm parametresini gereken şekilde ayarlamış olduğunuzdan emin olun. View parametresi, Azure haritalar Hizmetleri aracılığıyla hangi geopolitik tartışmalı içerik kümesini döndürüleceğini belirtir. 
>
>  Etkilenen Azure haritalar REST Hizmetleri:
>    
>    * Harita kutucuğunu al
>    * Harita görüntüsünü al 
>    * Aramayı bulanık al
>    * Arama POı al
>    * Arama POı kategorisini al
>    * Yakında arama alın
>    * Arama adresini al
>    * Yapılandırılmış arama adresini al
>    * Arama adresini tersine al
>    * Arama adresini al çapraz cadde
>    * Geometri Içinde arama sonrası
>    * Arama sonrası adres Batch önizlemesi
>    * Arama sonrası adres geri Batch önizlemesi
>    * Rota üzerinde arama sonrası
>    * Arama sonrası benzer toplu Iş önizlemesi
>
>    
>  **SDK**
>
>  Görünüm parametresini gereken şekilde ayarlamış olduğunuzdan ve Web SDK 'sının en son sürümüne sahip olduğunuzdan emin olun ve Android SDK. Etkilenen SDK 'lar:
>
>    * Azure Haritalar Web SDK 'Sı
>    * Azure Haritalar Android SDK


Azure Maps **Görünüm** parametresi ("Kullanıcı bölgesi parametresi" olarak da bilinir), bu ülkenin/bölgenin, Azure Maps aracılığıyla hangi geopolitik tartışmalı içeriği döndürüleceğini belirten doğru haritaları gösterecek olan ıkı harfli ıso-3166 ülke kodudur. harita üzerinde görünen Kenarlıklar ve Etiketler de dahil olmak üzere hizmetler. 

Varsayılan olarak görünüm parametresi, istekte tanımlanmasanız bile **birleştirilmiş**olarak ayarlanır. Kullanıcılarınızın konumunu tespit etmek ve sonra bu konum için görünüm parametresini doğru olarak ayarlamak sizin sorumluluğunuzdadır. Alternatif olarak, isteğin IP adresine göre harita verilerini döndürecek ' View = Auto ' ayarlama seçeneğiniz vardır.  Azure haritalar 'daki görünüm parametresi, Haritalar, görüntüler ve diğer verilerin yanı sıra Azure haritalar aracılığıyla erişmeye yetkili olan üçüncü taraf içeriklerin kullanılabilir hale getirilmesiyle ilgili olarak, geçerli yasaları ile uyumlu olmalıdır.


Aşağıdaki tabloda desteklenen görünümler sağlanmaktadır.

| Görüntüle         | Açıklama                            |  Haritalar | Arama | JS Harita Denetimi |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Birleşik Arap Emirlikleri (Arapça görünüm)    |   ✓   |        |     ✓          |
| AR           | Arjantin (argentinian görünümü)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahreyn (Arapça görünümü)                 |   ✓   |        |     ✓          |
| IN           | Hindistan (Hindistan görünümü)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (Arapça görünümü)                    |   ✓   |        |     ✓          |
| JO           | Ürdün (Arapça görünümü)                  |   ✓   |        |     ✓          |
| KW           | Kuveyt (Arapça görünümü)                  |   ✓   |        |     ✓          |
| LB           | Lübnan (Arapça görünümü)                 |   ✓   |        |     ✓          |
| MA           | Fas (Fas görüntüleyebilir)                |   ✓   |   ✓     |     ✓          |
| OM           | Umman (Arapça görünümü)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (Pasvahili tani görünümü)              |   ✓   |    ✓    |     ✓          |
| PS           | Filistin Yönetimi (Arapça görünüm)    |   ✓   |        |     ✓          |
| QA           | Katar (Arapça görünümü)                   |   ✓   |        |     ✓          |
| SA           | Suudi Arabistan (Arapça görünümü)            |   ✓   |        |     ✓          |
| SY           | Suriye (Arapça görünümü)                   |   ✓   |        |     ✓          |
| Vet           | Yemen (Arapça görünümü)                   |   ✓   |        |     ✓          |
| Otomatik         | İsteğin IP adresine göre harita verilerini döndürün.|   ✓   |    ✓   |     ✓          |
| Birleşik      | Birleşik görünüm (diğerleri)                  |   ✓   |   ✓     |     ✓          |
