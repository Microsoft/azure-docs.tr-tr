---
title: 'Konumlar ve bağlantı sağlayıcıları: Azure ExpressRoute | Microsoft Docs'
description: Bu makale, sunulan hizmetlerin konumları ve Azure bölgelerine nasıl bağlanılacağı hakkında ayrıntılı bir genel bakış sağlar. Konuma göre sıralanmıştır.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: cherylmc
ms.openlocfilehash: 60b5035506b8852c379f2e2617cdf918f09df010
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150073"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute ortakları ve eşleme konumları

> [!div class="op_single_selector"]
> * [Sağlayıcıya Göre Konumlar](expressroute-locations.md)
> * [Konuma Göre Sağlayıcılar](expressroute-locations-providers.md)


Bu makaledeki tablolarda, ExpressRoute coğrafi kapsamı ve konumları, ExpressRoute bağlantı sağlayıcıları ve ExpressRoute sistem tümleştiricileri (SIS) hakkında bilgi sağlanmaktadır.

> [!Note]
> Azure bölgeleri ve ExpressRoute konumları iki ayrı ve farklı kavramlardır ve bu iki arasındaki farkı anlamak, Azure hibrit ağ bağlantısını keşfetmek için kritik öneme sahiptir. 
>
>

## <a name="azure-regions"></a>Azure bölgeleri
Azure bölgeleri, Azure işlem, ağ ve depolama kaynaklarının bulunduğu küresel veri merkezlerindedir. Bir Azure kaynağı oluştururken müşterinin bir kaynak konumu seçmesini gerekir. Kaynak konumu, kaynağın oluşturulduğu Azure veri merkezini (veya kullanılabilirlik alanını) belirler.

## <a name="expressroute-locations"></a>ExpressRoute konumları
ExpressRoute konumları (bazen eşleme konumları olarak da adlandırılır veya karşılıyoruz), Microsoft Enterprise Edge (MSEE) cihazlarının bulunduğu ortak konum tesislerindedir. ExpressRoute konumları, Microsoft 'un ağı olan giriş noktasıdır ve küresel olarak dağıtılır ve müşterilere dünyanın dört bir yanındaki ağa bağlanma olanağı sağlar. Bu konumlar, ExpressRoute iş ortaklarının ve ExpressRoute Direct müşterilerinin Microsoft 'un ağına çapraz bağlantı vermesine neden olduğu yerdir. Genel olarak, ExpressRoute konumunun Azure bölgesiyle eşleşmesi gerekmez. Örneğin, bir müşteri, *Seattle* eşleme konumunda *Doğu ABD*kaynak konumuyla bir ExpressRoute devresi oluşturabilir.

Coğrafi bölge içindeki en az bir ExpressRoute konumuna bağlanırsanız coğrafi bölge içindeki tüm bölgeler arasında Azure hizmetlerine erişebileceksiniz. 

## <a name="locations"></a>Coğrafi bölge içindeki ExpressRoute konumlarına Azure bölgeleri
Aşağıdaki tablo, coğrafi bölge içindeki Azure bölgeler ile ExpressRoute konumları arasında yapılan eşlemeyi sağlar.

| **Jeopolitik bölge** | **Azure bölgeleri** | **ExpressRoute konumları** |
| --- | --- | --- |
| **Australia Government** | Avustralya Orta, Avustralya Orta 2 |Kanberra, Kanberra2 |
| **Avrupa** | Fransa Orta, Fransa Güney, Kuzey Avrupa, Batı Avrupa, UK Batı, UK Güney |Amsterdam, Amsterdam2, Copenhagen, Dublin, Frankfurt, Geneva, Londra, London2, Marseille, MILAN, Münih, Newport (Wales), Oslo, Paris, Stavanger, Stockholm, Zurich, Münih |
| **Kuzey Amerika** | Doğu ABD, Batı ABD, Doğu ABD 2, Batı ABD 2, Orta ABD, Orta Güney ABD, Orta Kuzey ABD, Batı Orta ABD, Orta Kanada, Doğu Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miamı, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, toron |
| **Asya** | Doğu Asya, Güneydoğu Asya |Hong Kong ÖIB, Jakarta, Kuala Lumpur, Singapur, Singapur2, Taipei |
| **Hindistan** | Batı Hindistan, Orta Hindistan, Güney Hindistan |Madras, Madras2, Bombay, Bombay2 |
| **Japonya** | Batı Japonya, Doğu Japonya |Osaka, Tokyo |
| **Okyanusya** | Güneydoğu Avustralya, Doğu Avustralya |Auckland, Melbourne, Perth, Sidney, Sydney2 | 
| **Güney Kore** | Kore Orta, Kore Güney |Busan, Seul|
| **BAE** | BAE Orta, BAE Kuzey | Dubai, Dubai2 |
| **Güney Afrika** | Güney Afrika Batı, Güney Afrika Kuzey |Cape Town, Johannesburg |
| **Güney Amerika** | Güney Brezilya |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Ulusal bulutlar için Azure bölgeleri ve geopolitik sınırlar
Aşağıdaki tablo ulusal bulutlar için bölgeler ve coğrafi sınırlar hakkında bilgi sağlar.

| **Jeopolitik bölge** | **Azure bölgeleri** | **ExpressRoute konumları** |
| --- | --- | --- |
| **ABD Hükümeti bulutu** |US Gov Arizona, US Gov Iowa, US Gov Teksas, US Gov Virginia, US DoD Orta, US DoD Doğu  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silikon Vadisi, Washington DC |
| **Çin Doğu** |Çin Doğu, Çin Doğu2 |Shanghai, Shanghai2 |
| **Çin Kuzey** |Çin Kuzey, Çin Kuzey2 |Pekin, Beijing2 |
| **Almanya** |Orta Almanya, Doğu Almanya |Berlin, Frankfurt |

Coğrafi bölgeler arasındaki bağlantı standart ExpressRoute SKU’da desteklenmiyor. Genel bağlantıyı desteklemek için ExpressRoute premium eklentisini etkinleştirmeniz gerekir. Ulusal bulut ortamlarına bağlantı desteklenmiyor. Bu tür bir ihtiyaç ortaya çıkarsa bağlantı sağlayıcınız ile çalışabilirsiniz.

## <a name="partners"></a>ExpressRoute bağlantı sağlayıcıları

Aşağıdaki tabloda bağlantı konumları ve her konum için hizmet sağlayıcıları gösterilmektedir. Hizmet sağlayıcılarını ve hizmet sunabildikleri konumları görüntülemek istiyorsanız bkz. [Hizmet sağlayıcısına göre konumlar](expressroute-locations.md).

* **Yerel Azure bölgeleri** , her bir eşleme konumundaki [ExpressRoute yerellerinin](expressroute-faqs.md) erişebileceği alanlardır. **yok** , ExpressRoute yerel 'in o eşleme konumunda kullanılabilir olmadığını gösterir.

* **Bölge** [fiyatlandırma](https://azure.microsoft.com/pricing/details/expressroute/)anlamına gelir.


### <a name="global-commercial-azure"></a>Küresel ticari Azure
| **Konum** | **Adresi** | **Bölge** | **Yerel Azure bölgeleri** | **ER doğrudan** | **Hizmet sağlayıcıları** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Batı Avrupa | 10G, 100G | Aryaka Networks, AT & T Netbonı, Ingiliz telekomünikasyon, Colt, Equinx, Eunetçalışmalar, GÉANT, Intercloud, ınterxiyon, KPN, x REACH, Level 3 Communications, Megaport, NTT Communications, turuncu, Tata Communications, Telefonica, Telenor, Telia taşıyıcısı, Verizon, Zayo |
| **Amsterdam2** | [Interxiyon AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Batı Avrupa | 10G, 100G | CenturyLink Cloud Connect, Colt, DE-CIX, Eunetçalışmalar, ınterxiyon, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | yok | yok | Equinix, Megaport |
| **Auckland** | [Vocus grubu NZ Albümno](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | yok | 'Yi | Devoli, Kordia, Megaport, Spark NZ, Vocus Group NZ |
| **Busan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Kore Güney | yok | LG CNS |
| **Kanberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Avustralya Orta | 'Yi | CDC |
| **Kanberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Avustralya Orta 2| yok | CDC |
| **Cape Town** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Güney Afrika Batı | 'Yi | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | Güney Hindistan | 'Yi | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | Güney Hindistan | yok | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | Orta Kuzey ABD | 10G, 100G | Aryaka Networks, & T Netbono, CenturyLink Cloud Connect, Geregix, Comcast, Coresıte, Equinix, Intercloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCFA Global Limited, Sprint, Telia taşıyıcısı, Verizon, Zayo |
| **Kopenhag** | [Interxiyon CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | yok | 'Yi | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | yok | 10G, 100G | Aryaka Networks, & T Netbonıngıgix, Equinix, Internet2, düzey 3 Communications, Megaport, nörona Networks, Telmex Unınet, Telia taşıyıcısı, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | Batı Orta ABD | yok | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | BAE Kuzey | yok | Etisalat UAE |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | BAE Kuzey | yok | du datamena, Megaport, Cumxcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Kuzey Avrupa | 10G, 100G | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | [Interxiyon FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Almanya Batı Orta | yok | DE-CIX, ınterxiyon, turuncu |
| **Cenevre** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | İsviçre Batı | 10G, 100G | |
| **Hong Kong ÖİB** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Doğu Asya | yok | Aryaka Networks, Ingiliz Teletası, CenturyLink Cloud Connect, baş Telem, Çin telekomünikasyon küresel, Equinx, Megaport, NTT Communications, turuncu, PCCW küresel Limited, Tata Iletişimleri, Telia taşıyıcısı, Verizon |
| **Cakarta** | Telkoa Endonezya | 4 | yok | 'Yi | |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Güney Afrika Kuzey | yok | İngiliz Telekoı, Internet çözümleri-bulut bağlantısı, sıvı telekomünikasyon, turuncu, Teraco |
| **Kuala Lumpur** | [ZAMAN dotCom Menara AIMS](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | yok | yok | TIME dotCom |
| **Las Vegas** | [Anahtar LV](https://www.switch.com/las-vegas) | 1 | yok | yok | CenturyLink Cloud Connect, Megaport |
| **Londra** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Birleşik Krallık Güney | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Telehouse Kuzey Iki](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | 1 | Birleşik Krallık Güney | 10G, 100G | X REACH, Equinx, Telehouse-KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | yok | yok | CoreSite, Equinx, Megaport, Neutrona Networks, NTT, Transtelco, Zayo |
| **Marsilya** |[Interxiyon MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Fransa Güney | yok | DE-CIX, ınterxiyon, Jaguar Network |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Avustralya Güneydoğu | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Ops, Telstra Corporation, TPG telekomünikasyon |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | yok | 'Yi | C3ntro, Equinix, Megaport, nörona Networks |
| **Milano** | [IRıDEOS](https://irideos.it/en/data-centers/) | 1 | yok | 'Yi | |
| **Montreal** | [Birlikte bulundurgix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | yok | yok | Bell Kanada, Kolombiya Gix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | Batı Hindistan | yok | Küresel CloudXchange (GCX), Reliance jıo, Sıfy, Tata Iletişimleri, Verizon |
| **Mumbai2** | Airtel | 2 | Batı Hindistan | yok | Airtel, Sify, Vodafone Idea |
| **Münih** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | yok | 10G, 100G | |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | yok | yok | CenturyLink Cloud Connect, Colt, Coresıte, Equinx, Intercloud, Megaport, paket, Zayo |
| **Newport(Galler)** | [Yeni Nesil Veriler](https://www.nextgenerationdata.co.uk) | 1 | Birleşik Krallık Batı | yok | İngiliz telekomünikasyon, Colt, düzey 3 Iletişimleri, yeni nesil veriler |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Japonya Batı | yok | Colt, Equinx, Internet girişimi Japonya Inc.-IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Norveç Doğu | 10G, 100G | Telenor |
| **Paris** | [Interxiyon PAR5](https://www.interxion.com/Locations/paris/) | 1 | Fransa Orta | yok | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, ınterxiyon, turuncu, Telia taşıyıcısı, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | yok | 'Yi | Megaport, NextDC |
| **Quebec City** | [Görüş](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Doğu Kanada | yok | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | Orta Güney ABD | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Güney Brezilya | yok | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | Batı ABD 2 | 10G, 100G | Aryaka Networks, Equinx, düzey 3 Communications, Megaport, Telus, Zayo |
| **Seul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Kore Orta | 10G, 100G | KINX, LG CNS, Sejong Telecom |
| **Silikon Vadisi** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | Batı ABD | 10G, 100G | Aryaka Networks, AT & T Netbonu, Ingiliz Teletası, CenturyLink Cloud Connect, Colt, Comcast, Coresıte, Equinix, Intercloud, Internet2, x REACH, paket, PacketFabric, Level 3 Communications, Megaport, turuncu, Sprint, Tata Iletişimleri, Telia taşıyıcısı, Verizon, Zayo |
| **Silicon Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | Batı ABD | 10G, 100G | Colt, Coresite | 
| **Singapur** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Güneydoğu Asya | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapur2** | [Genel anahtar Day Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Güneydoğu Asya | 10G, 100G | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Stavanger** | [Yeşil Sıradağlar DC1](https://greenmountain.no/dc1-stavanger/) | 1 | yok | 10G, 100G | |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | yok | 'Yi | Equinx, Telia taşıyıcısı |
| **Sidney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Avustralya Doğu | 10G, 100G | AARNet, AT & T Netbonı, Ingiliz telekomünikasyon, Devoli, Equinx, Kordia, Megaport, NEXTDC, NTT Communications, OPTI, turuncu, Spark NZ, Telstra Corporation, TPG Teley, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Avustralya Doğu | 10G, 100G | NextDC |
| **Taipei** | Baş Telem | 2 | yok | 'Yi | Baş Telem, FarEasTone |
| **Tokyo** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Japonya Doğu | 10G, 100G | Aryaka Networks, AT & T Netbonu, Ingiliz telekomünikasyon, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japonya Inc.-IIJ, NTT Communications, NTT Doğu, turuncu, Softbank, Verizon |
| **Toronto** | [Birlikte bulundurgix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Orta Kanada | 10G, 100G | & T Netbono, Bell Kanada, CenturyLink Cloud Connect, birlikte bulundurma, Equinx, x Reach Megaport, Telus, Verizon, Zayo |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Doğu ABD, Doğu ABD 2 | 10G, 100G | Aryaka Networks, AT & T Netbonu, Ingiliz Teletası, CenturyLink Cloud Connect, Geregix, Comcast, Coresite, Equınix, Internet2, Intercloud, Level 3 Communications, Megaport, Neutrona Networks,, NTT Communications, turuncu, PacketFabric, SES, Sprint, Tata İletişimler, Telia taşıyıcısı, Verizon, Zayo |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Doğu ABD, Doğu ABD 2 | 10G, 100G | Coresite, Viasat, Zayo | 
| **Zürih** | [Interxiyon ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | yok | yok | Intercloud, ınterxiyon, Swisscom |

 **+** çok yakında anlamına geliyor

### <a name="national-cloud-environments"></a>Ulusal bulut ortamları

Azure Ulusal bulutlar birbirinden ve küresel ticari bir Azure 'dan yalıtılmıştır. Bir Azure bulutu için ExpressRoute, diğer Azure bölgelerine bağlanamaz.

### <a name="us-government-cloud"></a>ABD bulutu
| **Konum** | **Adresi** | **Yerel Azure bölgeleri**| **ER doğrudan** | **Hizmet sağlayıcıları** |
| --- | --- | --- | --- | --- |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | yok | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | yok | 10G, 100G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | yok | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | ABD Devleti Arizona | yok | & T Netbono, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | ABD Devleti Texas | yok | CenturyLink Cloud Connect, Megaport |
| **Silikon Vadisi** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | yok | 10G, 100G | Equinix, Level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | yok | yok | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD Doğu, US Gov Virginia | 10G, 100G | & T Netbono, CenturyLink Cloud Connect, Equinx, düzey 3 Communications, Megaport, Verizon |

### <a name="china"></a>Çin
| **Konum** | **Hizmet sağlayıcıları** |
| --- | --- |
| **Pekin** |China Telecom |
| **Beijing2** | Çin Telem, GDS |
| **Şangay** |China Telecom |
| **Shanghai2** | Çin Telem, GDS |

Daha fazla bilgi için bkz. [Çin’de ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Almanya
| **Konum** | **Hizmet sağlayıcıları** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Exchange sağlayıcıları üzerinden bağlantı
Bağlantı sağlayıcınız önceki bölümlerde listelenmemişse hala bağlantı oluşturabilirsiniz.

* Yukarıdaki tabloda yer alan değişimlerin herhangi birine bağlı olup olmadığını görmek için bağlantı sağlayıcınıza başvurun. Değişim sağlayıcıları tarafından sunulan hizmetler hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kontrol edebilirsiniz. Birkaç bağlantı sağlayıcı Ethernet değişimlerine zaten bağlı.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Bağlantı sağlayıcınızı, ağınızı seçtiğiniz eşleme konumuna genişletmesini sağlayın.
  * Bağlantı sağlayıcınızın bağlantınızı yüksek oranda kullanılabilir şekilde genişlettiğinden emin olun, böylece hiç tek nokta arızası olmaz.
* Microsoft’a bağlanmak için bağlantı sağlayınız olarak değişime sahip bir ExpressRoute bağlantı hattı sipariş edin.
  * Bağlantı kurmak için [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-classic.md)’daki adımları izleyin.

## <a name="connectivity-through-satellite-operators"></a>Uydu işleçleri üzerinden bağlantı
Uzaktan çalışıyorsanız ve fiber bağlantınız yoksa veya diğer bağlantı seçeneklerini araştırmak istiyorsanız aşağıdaki uydu işleçlerini kontrol edebilirsiniz. 

* Intelsat
* [IR](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>Ek hizmet sağlayıcıları üzerinden bağlantı
| **Konum** | **Exchange** | **Bağlantı sağlayıcıları** |
| --- | --- | --- |
| **Amsterdam** | Equinx, ınterxiyon, düzey 3 Iletişimleri | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, Gulf Köprüsü International, Kalaam Telebel B. S. C, MainOne, Nianet, POST Telepı, Proximus, TDC Erhverv, Telekom |
| **Atlanta** | Equinix| Crown kalele
| **Cape Town** | Teraco | MTN |
| **Chicago** | Equinix| Crown kalele, SPI Enterprise, WINI akışı |
| **Dallas** | Equinix, Megaport | AXTEL, C3ntro Teley, Cox Iş, Crown, veri bulunan |
| **Frankfurt** | Interxion | BICS, Cinia, Nianet, QSC AG, Telekod Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hong Kong ÖİB** | Equinix | Başkan, makro görünümü telekomünikasyon |
| **Johannesburg** | Teraco | MTN |
| **Londra** | Bics, equinx, eunetçalışma| Bezeq International Ltd., CoreAzure, Epsilon telekomünikasyon Limited, üstel E, HSO, NexGen Networks, Proximus, Tamares Teley, Zain |
| **Los Angeles** | Equinix |Crown kalele, SPI kurumsal, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Aptum teknolojileri, Roger, ZiRrO |
| **New York** |Equinix, Megaport | ALCE Iş, Crown role, SPI Enterprise, Webair |
| **Paris** | Equinix | Proximus |
| **Quebec City** | Megaport | Fibrenoire |
| **Sao Paula** | Equinix | Venha PRA Nua |
| **Seattle** |Equinix | Alaska Communications |
| **Silikon Vadisi** |Coresite, Equinx | Cox Business, SPIN Enterprise, WINI Stream, X2nsat Inc. |
| **Singapur** |Equinix |1CLOUDSTAR, BICS, CMC telekomünikasyon, Epsilon telekomünikasyon Limited, LGA telekomünikasyon, Birleşik bilgi Highway (UıH) |
| **Slough** | Equinix | HSO|
| **Sidney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Roger, Thinktel, ZiRrO|
| **Washington DC** |Equinix | Allet Iş, BICS, Cox Iş, Crown, GTT Communications Inc, Epsilon telekomünikasyon Limited, Masergy, Wınsırla |

## <a name="expressroute-system-integrators"></a>ExpressRoute sistem tümleştiricileri
İhtiyaçlarınıza uyan özel bağlantıyı etkinleştirme ağınızın ölçeğine bağlı olarak zorlu olabilir. ExpressRoute’a yönelik ekleme işleminde size yardımcı olmak üzere aşağıdaki tabloda listelenen herhangi bir sistem tümleştirici ile çalışabilirsiniz.

| **Continent** | **Sistem tümleştiricileri** |
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
