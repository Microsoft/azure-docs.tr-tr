---
title: 'Konumlar ve bağlantı sağlayıcıları: Azure ExpressRoute | Microsoft Docs'
description: Bu makale, sunulan hizmetlerin konumları ve Azure bölgelerine nasıl bağlanılacağı hakkında ayrıntılı bir genel bakış sağlar. Konuma göre sıralanmıştır.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/10/2021
ms.author: duau
ms.openlocfilehash: 5972e95425513e789725ed8de142c721d58011f3
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739935"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute ortakları ve eşleme konumları

> [!div class="op_single_selector"]
> * [Sağlayıcıya göre konumlar](expressroute-locations.md)
> * [Konuma göre sağlayıcılar](expressroute-locations-providers.md)


Bu makaledeki tablolarda, ExpressRoute coğrafi kapsamı ve konumları, ExpressRoute bağlantı sağlayıcıları ve ExpressRoute sistem tümleştiricileri (SIS) hakkında bilgi sağlanmaktadır.

> [!Note]
> Azure bölgeleri ve ExpressRoute konumları iki ayrı ve farklı kavramlardır ve bu iki arasındaki farkı anlamak, Azure hibrit ağ bağlantısını keşfetmek için kritik öneme sahiptir. 
>
>

## <a name="azure-regions"></a>Azure bölgeleri
Azure bölgeleri, Azure işlem, ağ ve depolama kaynaklarının bulunduğu küresel veri merkezlerindedir. Bir Azure kaynağı oluştururken müşterinin bir kaynak konumu seçmesini gerekir. Kaynak konumu, kaynağın oluşturulduğu Azure veri merkezini (veya kullanılabilirlik alanını) belirler.

## <a name="expressroute-locations"></a>ExpressRoute konumları
ExpressRoute konumları (bazen eşleme konumları olarak da adlandırılır veya karşılıyoruz), Microsoft Enterprise Edge (MSEE) cihazlarının bulunduğu ortak konum tesislerindedir. ExpressRoute konumları, Microsoft 'un ağı olan giriş noktasıdır ve küresel olarak dağıtılır ve müşterilere dünyanın dört bir yanındaki ağa bağlanma olanağı sağlar. Bu konumlar, ExpressRoute iş ortaklarının ve ExpressRoute Direct müşterilerinin Microsoft 'un ağına çapraz bağlantı vermesine neden olduğu yerdir. Genel olarak, ExpressRoute konumunun Azure bölgesiyle eşleşmesi gerekmez. Örneğin, bir müşteri, *Seattle* eşleme konumunda *Doğu ABD* kaynak konumuyla bir ExpressRoute devresi oluşturabilir.

Coğrafi bölge içindeki en az bir ExpressRoute konumuna bağlanırsanız coğrafi bölge içindeki tüm bölgeler arasında Azure hizmetlerine erişebileceksiniz. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Bir coğrafi bölge içindeki Azure bölgeler ile ExpressRoute konumları arasında eşleme
Aşağıdaki tablo, coğrafi bölge içindeki Azure bölgeler ile ExpressRoute konumları arasında yapılan eşlemeyi sağlar.

| **Geopolitik bölge** | **Azure bölgeleri** | **ExpressRoute konumları** |
| --- | --- | --- |
| **Australia Government** | Orta Avustralya, Orta Avustralya 2 |Kanberra, Kanberra2 |
| **Avrupa** | Fransa Orta, Fransa Güney, Almanya Kuzey, Almanya Orta Batı, Kuzey Avrupa, Norveç Doğu, Norveç Batı, İsviçre Kuzey, İsviçre Batı, UK Batı, UK Güney, Batı Avrupa |Amsterdam, Amsterdam2, Berlin, Copenhagen, Dublin, Frankfurt, Frankfurt2, Geneva, Londra, London2, Madrid, Marseille, MILAN, Münih, Newport (Wales), Oslo, Paris, Stavanger, Stockholm, Zurich |
| **Kuzey Amerika** | Doğu ABD, Batı ABD, Doğu ABD 2, Batı ABD 2, Orta ABD, Orta Güney ABD, Orta Kuzey ABD, Orta Batı ABD, Orta Kanada, Doğu Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Los Angeles2, MIAMI, Minneapolis, Montreal, New York, Phoenix, Quebec City, Queretaro (Meksika), Quincy, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Toron, Toronto2, Vantaya ver, Washington DC, Washington DC2 |
| **Asya** | Doğu Asya, Güneydoğu Asya | Bangkok, Hong Kong, Hong Kong2, Jakarta, Kuala Lumpur, Singapur, Singapur2, Taipei |
| **Hindistan** | Hindistan Batı, Hindistan Orta, Hindistan Güney |Madras, Madras2, Bombay, Bombay2 |
| **Japonya** | Batı Japonya, Doğu Japonya |Osaka, Tokyo, Tokyo2 |
| **Okyanusya** | Güneydoğu Avustralya, Doğu Avustralya |Auckland, Melbourne, Perth, Sidney, Sydney2 | 
| **Güney Kore** | Güney Kore - Orta, Güney Kore - Güney |Busan, Seul|
| **BAE** | BAE Orta, BAE Kuzey | Dubai, Dubai2 |
| **Güney Afrika** | Güney Afrika Batı, Güney Afrika Kuzey |Cape Town, Johannesburg |
| **Güney Amerika** | Güney Brezilya |Bogota, RIO ve Içeeiro, Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Ulusal bulutlar için Azure bölgeleri ve geopolitik sınırlar
Aşağıdaki tablo ulusal bulutlar için bölgeler ve coğrafi sınırlar hakkında bilgi sağlar.

| **Geopolitik bölge** | **Azure bölgeleri** | **ExpressRoute konumları** |
| --- | --- | --- |
| **ABD bulutu** |US Gov Arizona, US Gov Iowa, US Gov Teksas, US Gov Virginia, Orta US DoD, Doğu US DoD  |Atlanta, Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Doğu Çin** |Doğu Çin, Doğu Çin2 |Shanghai, Shanghai2 |
| **Kuzey Çin** |Kuzey Çin, Kuzey Çin2 |Pekin, Beijing2 |
| **Almanya** |Orta Almanya, Almanya Doğu |Berlin, Frankfurt |

Coğrafi bölgeler arasındaki bağlantı standart ExpressRoute SKU’da desteklenmiyor. Genel bağlantıyı desteklemek için ExpressRoute premium eklentisini etkinleştirmeniz gerekir. Ulusal bulut ortamlarına bağlantı desteklenmiyor. Bu tür bir ihtiyaç ortaya çıkarsa bağlantı sağlayıcınız ile çalışabilirsiniz.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute bağlantı sağlayıcıları

Aşağıdaki tabloda bağlantı konumları ve her konum için hizmet sağlayıcıları gösterilmektedir. Hizmet sağlayıcılarını ve hizmet sunabildikleri konumları görüntülemek istiyorsanız bkz. [Hizmet sağlayıcısına göre konumlar](expressroute-locations.md).

* **Yerel Azure bölgeleri** , her bir eşleme konumundaki [ExpressRoute yerellerinin](expressroute-faqs.md) erişebileceği alanlardır. **yok** , ExpressRoute yerel 'in o eşleme konumunda kullanılabilir olmadığını gösterir.

* **Bölge** [fiyatlandırma](https://azure.microsoft.com/pricing/details/expressroute/)anlamına gelir.


### <a name="global-commercial-azure"></a>Küresel ticari Azure
| **Konum** | **Adres** | **Bölge** | **Yerel Azure bölgeleri** | **ER doğrudan** | **Hizmet sağlayıcıları** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | West Europe | 10G, 100G | Aryaka Networks, AT&T Netbonı, Ingiliz telekomünikasyon, Colt, Equinx, Eunetçalışmalar, GÉANT, Intercloud, ınterxiyon, KPN, x REACH, Level 3 Communications, Megaport, NTT Communications, turuncu, Tata Communications, Telefonica, Telenor, Telia taşıyıcısı, Verizon, Zayo |
| **Amsterdam2** | [Interxiyon AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | West Europe | 10G, 100G | İngiliz Teley, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, Eunetçalışmalar, GÉANT, ınterxiyon, No, NTT küresel veri merkezleri EMEA, turuncu, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | yok | 10G, 100G | Equinix, Megaport |
| **Auckland** | [Vocus grubu NZ Albümno](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | yok | 'Yi | Devoli, Kordia, Megaport, REANNZ, Spark NZ, Vocus Group NZ |
| **Bangkok** | [AıS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | yok | 'Yi | A,, UıH |
| **Berlin** | [NTT GDC](https://www.e-shelter.de/en/location/berlin-1-data-center) | 1 | Almanya Kuzey | 'Yi | Colt, Equinx, NTT küresel veri merkezleri EMEA|
| **Bogota** | [Equinix BG1](https://www.equinix.com/locations/americas-colocation/colombia-colocation/bogota-data-centers/bg1/) | 4 | yok | 'Yi | Equinix |
| **Busan** | [LG CNS](https://www.lgcns.com/En/Service/DataCenter) | 2 | Güney Kore - Güney | yok | LG CNS |
| **Kanberra** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | Orta Avustralya | 10G, 100G | CDC |
| **Kanberra2** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | Orta Avustralya 2| 10G, 100G | CDC, Equinix |
| **Cape Town** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Güney Afrika - Batı | 'Yi | BCX, Internet çözümleri-bulut bağlantısı, sıvı telekomünikasyon, Teraco |
| **Chennai** | Tata Communications | 2 | Güney Hindistan | 'Yi | BSNL, küresel CloudXchange (GCX), SıFY, Tata Iletişimleri, Vodavfoneidea |
| **Chennai2** | Airtel | 2 | Güney Hindistan | 'Yi | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | Orta Kuzey ABD | 10G, 100G | Aryaka Networks, AT&T Netbonu, Ingiliz telekomünikasyon, CenturyLink Cloud Connect, Geregix, Colt, Comcast, Coresıte, Equinix, Intercloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCFA Global Limited, Sprint, Telia taşıyıcısı, Verizon, Zayo |
| **Kopenhag** | [Interxiyon CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | yok | 'Yi | Interxion |
| **Şubesi** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | yok | 10G, 100G | Aryaka Networks,&T Netbonıngıgix, Equinix, Internet2, düzey 3 Communications, Megaport, Neutrona Networks, PacketFabric, Telmex Unınet, Telia taşıyıcısı, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | Orta Batı ABD | yok | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | BAE Kuzey | yok | Etisalat UAE |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | BAE Kuzey | yok | DE-CIX, du datamena, Equinx, Megaport, turuncu, Cumxcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Kuzey Avrupa | 10G, 100G | CenturyLink Cloud Connect, Colt, EAR, Equinix, GEANT, Eunetçalışmalar, ınterxiyon, Megaport |
| **Frankfurt** | [Interxiyon FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Almanya Orta Batı | 10G, 100G | &T Netbono, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, Eunetçalışma, GEANT, Intercloud, ınterxiyon, Megaport, turuncu, Telia taşıyıcısı, T-Systems |
| **Frankfurt2** | [Equinix FR7](https://www.equinix.com/locations/europe-colocation/germany-colocation/frankfurt-data-centers/fr7/) | 1 | Almanya Orta Batı | 10G, 100G | Equinix |
| **Cenevre** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | İsviçre Batı | 10G, 100G | Equinx, Megaport, Swisscom |
| **Hong Kong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Doğu Asya | 'Yi | Aryaka Networks, Ingiliz Teletası, CenturyLink Cloud Connect, baş Telem, Çin telekomünikasyon küresel, Çin Unicom, Colt, Equinix, Intercloud, Megaport, NTT Communications, turuncu, PCCW küresel Limited, Tata Iletişimleri, Telia taşıyıcısı, Verizon |
| **Hong Kong2** | [ıadvantage MEGA-ı](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | Doğu Asya | 'Yi | Çin Mobile International, Çin telekomünikasyon küresel, ıadvantage, Megaport, PCFA küresel sınırlı, SingTel |
| **Cakarta** | Telın, Telkoa Endonezya | 4 | yok | 'Yi | Telın |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Güney Afrika - Kuzey | 'Yi | BCX, Ingiliz Telekoı, Internet çözümleri-bulut bağlantısı, sıvı telekomünikasyon, turuncu, Teraco |
| **Kuala Lumpur** | [ZAMAN dotCom Menara AIMS](https://www.time.com.my/enterprise/connectivity/direct-cloud) | 2 | yok | yok | TIME dotCom |
| **Las Vegas** | [Anahtar LV](https://www.switch.com/las-vegas) | 1 | yok | 10G, 100G | CenturyLink Cloud Connect, Megaport, PacketFabric |
| **Londra** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Güney Birleşik Krallık | 10G, 100G | &T Netbononun, Ingiliz telekomünikasyon, Colt, Equinx, Eunetçalışmalar, Intercloud, Internet Solutions-Cloud Connect, ınterxiyon, JISC, Level 3 Iletişimleri, Megaport, MTN, NTT Communications, turuncu, PCCW küresel Limited, Tata Iletişimleri, Telehouse-KDDI, Telenor, Telia taşıyıcısı, Verizon, Vodafone, Zayo |
| **London2** | [Telehouse Kuzey Iki](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Güney Birleşik Krallık | 10G, 100G | İngiliz telek, CenturyLink Cloud Connect, Colt, GTT, x REACH, Equınix, Megaport, SES, Sohonet, Telehouse-KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | yok | 10G, 100G | CoreSite, Equinx, Megaport, Neutrona Networks, NTT, Zayo |
| **Los Angeles2** | [Equinix LA1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/los-angeles-data-centers/la1/) | 1 | yok | 10G, 100G | Equinix |
| **Madrid** | [Interxiyon MAD1](https://www.interxion.com/es/donde-estamos/europa/madrid) | 1 | West Europe | 10G, 100G | Interxion |
| **Marsilya** |[Interxiyon MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Güney Fransa | yok | DE-CIX, GEANT, ınterxiyon, Jaguar Network, Ooredoo Cloud Connect |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Güneydoğu Avustralya | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Ops, Telstra Corporation, TPG telekomünikasyon |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | yok | 10G, 100G | Claro, C3ntro, Equinix, Megaport, nörona Networks |
| **Milano** | [IRıDEOS](https://irideos.it/en/data-centers/) | 1 | yok | 'Yi | Colt, Equinx, Fastweb, ıRıDEOS, Reteaydınlatma |
| **Minneapolis** | [Birlikte bulundurgix MIN1](https://www.cologix.com/data-centers/minneapolis/min1/) | 1 | yok | 10G, 100G | Birlikte bulundurma, Megaport |
| **Montreal** | [Birlikte bulundurgix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | yok | 10G, 100G | Bell Kanada, Kolombiya Gix, Fibrenoire, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | Batı Hindistan | 'Yi | BSNL, DE-CIX, Global CloudXchange (GCX), Reliance jıo, Sıfy, Tata Communications, Verizon |
| **Bombay2** | Airtel | 2 | Batı Hindistan | 'Yi | Airtel, Sify, Vodafone Idea |
| **Münih** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/munich/) | 1 | yok | 'Yi | DE-CıX |
| **New Yok** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | yok | 10G, 100G | CenturyLink Cloud Connect, Colt, Coresite, DE-CIX, Equinx, Intercloud, Megaport, paket, Zayo |
| **Newport (Wales)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | Batı Birleşik Krallık | yok | İngiliz telekomünikasyon, Colt, JISC, düzey 3 Iletişimleri, yeni nesil veriler |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Batı Japonya | 10G, 100G | TOKYO, Colt, Equinx, Internet girişimi Japonya Inc.-IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank, Tokai Communications |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Norveç Doğu | 10G, 100G | GlobalConnect, Megaport, Telenor, Telia taşıyıcısı |
| **Paris** | [Interxiyon PAR5](https://www.interxion.com/Locations/paris/) | 1 | Orta Fransa | 10G, 100G | İngiliz Teley, CenturyLink Cloud Connect, Colt, Equinix, Intercloud, ınterxiyon, Jaguar Network, Megaport, turuncu, Telia taşıyıcısı, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | yok | 'Yi | Megaport, NextDC |
| **Phoenix** | [EdgeConneX PHX01](https://www.edgeconnex.com/locations/north-america/phoenix-az/) | 1 | yok | 10G, 100G | |
| **Quebec City** | [Görüş](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Doğu Kanada | 10G, 100G | Bell Kanada, Megaport, Telus |
| **Queretaro (Meksika)** | [KIO Networks QR01](https://www.kionetworks.com/es-mx/) | 4 | yok | 'Yi | Transtelco|
| **Quincy** | [Sabey veri merkezi-oluşturma](https://sabeydatacenters.com/data-center-locations/central-washington-data-centers/quincy-data-center) | 1 | Batı ABD 2 | 10G, 100G | |
| **Rio de Janeiro** | [Equinix-RJ2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/rio-de-janeiro-data-centers/rj2/) | 3 | Brezilya Güneydoğu | 'Yi | Equinix |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | Orta Güney ABD | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Güney Brezilya | 10G, 100G | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | Batı ABD 2 | 10G, 100G | Aryaka Networks, Equinx, düzey 3 Communications, Megaport, Telus, Zayo |
| **Seul** | [KINX Gasan IDC](https://www.kinx.net/?lang=en) | 2 | Güney Kore - Orta | 10G, 100G | KINX, KT, LG CNS, Equinix, Sejong telekomünikasyon |
| **Silikon Vadisi** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | Batı ABD | 10G, 100G | Aryaka Networks, AT&T Netbonı, Ingiliz telekomünikasyon, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equınx, Intercloud, Internet2, x REACH, paket, PacketFabric, Level 3 Communications, Megaport, turuncu, Sprint, Tata Communications, Telia taşıyıcısı, Verizon, Zayo |
| **Silicon Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | Batı ABD | 10G, 100G | Colt, Coresite | 
| **Singapur** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Güneydoğu Asya | 10G, 100G | Aryaka Networks, AT&T Netbonu, Ingiliz Teleası, Çin Mobile International, Epsilon Global Communications, Equinix, Intercloud, Level 3 Communications, Megaport, NTT Communications, turuncu, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodadfone |
| **Singapur2** | [Genel anahtar Day Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Güneydoğu Asya | 10G, 100G | Çin Unicom Global, Colt, Epsilon Global Communications, Megaport, PCFA küresel sınırlı, SingTel, Telehouse-KDDI |
| **Stavanger** | [Yeşil Sıradağlar DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Norveç Batı | 10G, 100G |GlobalConnect, Megaport |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | yok | 'Yi | Equinx, Telia taşıyıcısı |
| **Sidney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Doğu Avustralya | 10G, 100G | AARNet, AT&T Netbonı, Ingiliz telekomünikasyon, Devoli, Equinx, Kordia, Megaport, NEXTDC, NTT Communications, OPTI, turuncu, Spark NZ, Telstra Corporation, TPG Teley, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Doğu Avustralya | 10G, 100G | Megaport, NextDC |
| **Taipei** | Baş Telem | 2 | yok | 'Yi | Baş Telem, Chunghwa telekomünikasyon, FarEasTone |
| **Tokyo** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Doğu Japonya | 10G, 100G | Aryaka Networks, AT&T Netbonı, BBIX, Ingiliz telekomünikasyon, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japonya Inc.-IIJ, Megaport, NTT Communications, NTT Doğu, turuncu, Softbank, Verizon |
| **Tokyo2** | [TOKYO 'DA](https://www.attokyo.com/) | 2 | Doğu Japonya | 10G, 100G | TOKYO, Megaport, Tokai Iletişimleri |
| **Toronto** | [Birlikte bulundurgix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Orta Kanada | 10G, 100G | &T Netbono, Bell Kanada, CenturyLink Cloud Connect, birlikte bulundurma, Equinx, x Reach Megaport, Telus, Verizon, Zayo |
| **Toronto2** | [Allied REIT](https://www.alliedreit.com/property/905-king-st-w/) | 1 | Orta Kanada | 10G, 100G | |
| **Vancouver** | [Birlikte bulundurgix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | yok | 'Yi | Birlikte bulundurgix, Megaport, Telus |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Doğu ABD, Doğu ABD 2 | 10G, 100G | Aryaka Networks,&T Netbonı Ingiliz sev, CenturyLink Cloud Connect, Geregix, Colt, Comcast, Coresıte, Equinix, Internet2, Intercloud, Iron Sıradağlar, x REACH, düzey 3 Iletişimleri, Megaport, Neutrona Networks |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-center-locations/northern-virginia-washington-dc) | 1 | Doğu ABD, Doğu ABD 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Intelsat, Megaport, Viasat, Zayo | 
| **Zürih** | [Interxiyon ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | İsviçre Kuzey | 10G, 100G | Colt, Equinx, Intercloud, ınterxiyon, Megaport, Swisscom |

 **+** çok yakında geliyor

### <a name="national-cloud-environments"></a>Ulusal bulut ortamları

Azure Ulusal bulutlar birbirinden ve küresel ticari Azure 'dan yalıtılmıştır. Bir Azure bulutu için ExpressRoute, diğer Azure bölgelerine bağlanamaz.

### <a name="us-government-cloud"></a>ABD bulutu
| **Konum** | **Adres** | **Yerel Azure bölgeleri**| **ER doğrudan** | **Hizmet sağlayıcıları** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | yok | 10G, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | yok | 10G, 100G | &T Netbononun, Ingiliz telekomünikasyon, Equinx, düzey 3 Communications, Verizon |
| **Şubesi** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | yok | 10G, 100G | Equinix, Megaport, Verizon |
| **New Yok** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | yok | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov Arizona | 10G, 100G | &T Netbono, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov Texas | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Silikon Vadisi** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | yok | 10G, 100G | &T, Equinx, düzey 3 Iletişimleri, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | yok | 10G, 100G | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD Doğu, US Gov Virginia | 10G, 100G | &T Netbono, CenturyLink Cloud Connect, Equinx, düzey 3 Communications, Megaport, Verizon |

### <a name="china"></a>Çin
| **Konum** | **Adres** | **Yerel Azure bölgeleri** | **ER doğrudan** | **Hizmet sağlayıcıları** |
| --- | --- | --- | --- | --- |
| **Pekin** | China Telecom | yok | 'Yi | China Telecom |
| **Beijing2** | GDS | yok | 'Yi | Çin Telem, Çin Unicom, GDS |
| **Shanghai** | China Telecom | yok | 'Yi | China Telecom |
| **Shanghai2** | GDS | yok | 'Yi | Çin Telem, Çin Unicom, GDS |

Daha fazla bilgi için bkz. [Çin 'de ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Almanya
| **Konum** | **Hizmet sağlayıcıları** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Exchange sağlayıcıları üzerinden bağlantı
Bağlantı sağlayıcınız önceki bölümlerde listelenmemişse hala bağlantı oluşturabilirsiniz.

* Yukarıdaki tabloda yer alan değişimlerin herhangi birine bağlı olup olmadığını görmek için bağlantı sağlayıcınıza başvurun. Değişim sağlayıcıları tarafından sunulan hizmetler hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kontrol edebilirsiniz. Birkaç bağlantı sağlayıcı Ethernet değişimlerine zaten bağlı.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CıX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Bağlantı sağlayıcınızı, ağınızı seçtiğiniz eşleme konumuna genişletmesini sağlayın.
  * Bağlantı sağlayıcınızın bağlantınızı yüksek oranda kullanılabilir şekilde genişlettiğinden emin olun, böylece hiç tek nokta arızası olmaz.
* Microsoft’a bağlanmak için bağlantı sağlayınız olarak değişime sahip bir ExpressRoute bağlantı hattı sipariş edin.
  * Bağlantı kurmak için [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-classic.md)’daki adımları izleyin.

## <a name="connectivity-through-satellite-operators"></a>Uydu işleçleri üzerinden bağlantı
Uzaktan çalışıyorsanız ve fiber bağlantınız yoksa veya diğer bağlantı seçeneklerini araştırmak istiyorsanız aşağıdaki uydu işleçlerini kontrol edebilirsiniz. 

* Intelsat
* [IR](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Ek hizmet sağlayıcıları üzerinden bağlantı
| **Konum** | **cihazlar** | **Bağlantı sağlayıcıları** |
| --- | --- | --- |
| **Amsterdam** | Equinx, ınterxiyon, düzey 3 Iletişimleri | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, Gulf Köprüsü International, Kalaam Telebel B. S. C, MainOne, Nianet, POST Telepı, Proximus, RETN, TDC Erhverv, telekomünikasyon Italia parlak Le, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Crown kalele
| **Cape Town** | Teraco | MTN |
| **Chennai** | Tata Communications | Tata TeleServices |
| **Chicago** | Equinix| Crown kalele, SPI Enterprise, WINI akışı |
| **Şubesi** | Equinix, Megaport | AXTEL, C3ntro Teley, Cox Iş, Crown, veri bulunan |
| **Frankfurt** | Interxion | BICS, Cinia, Equinx, Nianet, QSC AG, Telekod Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hong Kong ÖİB** | Equinix | Başkan, makro görünümü telekomünikasyon |
| **Johannesburg** | Teraco | MTN |
| **Londra** | Bics, equinx, eunetçalışma| Bezeq International Ltd., CoreAzure, Epsilon telekomünikasyon Limited, üstel E, HSO, NexGen Networks, Proximus, Tamares Teley, Zain |
| **Los Angeles** | Equinix |Crown kalele, SPI kurumsal, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix| Airgate Technologies, Inc. Aptum teknolojileri, Roger, ZiRrO |
| **Mumbai** | Tata Communications | Tata TeleServices |
| **New Yok** |Equinix, Megaport | ALCE Iş, Crown role, SPI Enterprise, Webair |
| **Paris** | Equinix | Proximus |
| **Quebec City** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silikon Vadisi** |Coresite, Equinx | Cox Business, SPIN Enterprise, WINI Stream, X2nsat Inc. |
| **Singapur** |Equinix |1CLOUDSTAR, BICS, CMC telekomünikasyon, Epsilon telekomünikasyon Limited, LGA telekomünikasyon, Birleşik bilgi Highway (UıH) |
| **Slough** | Equinix | HSO|
| **Sidney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Oncore Cloud Services Inc., Roger, Thinktel, ZiRrO|
| **Washington DC** |Equinix | Allet Iş, BICS, Cox Iş, Crown, GTT Communications Inc, Epsilon telekomünikasyon Limited, Masergy, Wınsırla |

## <a name="expressroute-system-integrators"></a>ExpressRoute sistem tümleştiricileri
İhtiyaçlarınıza uyan özel bağlantıyı etkinleştirme ağınızın ölçeğine bağlı olarak zorlu olabilir. ExpressRoute’a yönelik ekleme işleminde size yardımcı olmak üzere aşağıdaki tabloda listelenen herhangi bir sistem tümleştirici ile çalışabilirsiniz.

| **Kıta** | **Sistem tümleştiriciler** |
| --- | --- |
| **Asya** |Avanade Inc., OneAs1a |
| **Avustralya** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Avrupa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Kuzey Amerika** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Güney Amerika** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Sonraki adımlar
* ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).
* Tüm önkoşulların sağlandığından emin olun. Bkz. [ExpressRoute önkoşulları](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Konum eşleme"
