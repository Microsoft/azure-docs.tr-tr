---
title: 'Bağlantı sağlayıcıları ve konumları: Azure ExpressRoute | Microsoft Docs'
description: Bu makale, sunulan hizmetlerin konumları ve Azure bölgelerine nasıl bağlanılacağı hakkında ayrıntılı bir genel bakış sağlar. Bağlantı sağlayıcısına göre sıralanır.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/10/2021
ms.author: duau
ms.openlocfilehash: 741fd7e4554ffe07de08e96233c440210eefa2fa
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498416"
---
# <a name="expressroute-connectivity-partners-and-peering-locations"></a>ExpressRoute bağlantı iş ortakları ve eşleme konumları

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

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Coğrafi bölge içindeki Azure bölgelerini ExpressRoute konumlarına yönlendirin.
Aşağıdaki tablo, coğrafi bölge içindeki Azure bölgeler ile ExpressRoute konumları arasında yapılan eşlemeyi sağlar.

| **Geopolitik bölge** | **Azure bölgeleri** | **ExpressRoute konumları** |
| --- | --- | --- |
| **Australia Government** |Orta Avustralya, Orta Avustralya 2 |Kanberra, Kanberra2 |
| **Avrupa** | Fransa Orta, Fransa Güney, Almanya Kuzey, Almanya Orta Batı, Kuzey Avrupa, Norveç Doğu, Norveç Batı, İsviçre Kuzey, İsviçre Batı, UK Batı, UK Güney, Batı Avrupa |Amsterdam, Amsterdam2, Berlin, Copenhagen, Dublin, Frankfurt, Frankfurt2, Geneva, Londra, London2, Madrid, Marseille, MILAN, Münih, Newport (Wales), Oslo, Paris, Stavanger, Stockholm, Zurich |
| **Kuzey Amerika** |Doğu ABD, Batı ABD, Doğu ABD 2, Batı ABD 2, Orta ABD, Orta Güney ABD, Orta Kuzey ABD, Orta Batı ABD, Orta Kanada, Doğu Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Los Angeles2, MIAMI, Minneapolis, Montreal, New York, Phoenix, Quebec City, Queretaro (Meksika), Quincy, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Toron, Toronto2, Vantaya ver, Washington DC, Washington DC2 |
| **Noktası** | Doğu Asya, Güneydoğu Asya | Bangkok, Hong Kong, Hong Kong2, Jakarta, Kuala Lumpur, Singapur, Singapur2, Taipei |
| **Hindistan** | Hindistan Batı, Hindistan Orta, Hindistan Güney |Madras, Madras2, Bombay, Bombay2 |
| **Japonya** | Batı Japonya, Doğu Japonya |Osaka, Tokyo, Tokyo2 |
| **Okyanusya** | Güneydoğu Avustralya, Doğu Avustralya |Auckland, Melbourne, Perth, Sidney, Sydney2 |
| **Güney Kore** | Güney Kore - Orta, Güney Kore - Güney |Busan, Seul|
| **BAE** | BAE Orta, BAE Kuzey | Dubai, Dubai2 |
| **Güney Afrika** | Güney Afrika Batı, Güney Afrika Kuzey |Cape Town, Johannesburg |
| **Güney Amerika** | Güney Brezilya |Bogota, RIO ve Içeeiro, Sao Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Ulusal bulutlar için bölgeler ve coğrafi sınırlar
Aşağıdaki tablo ulusal bulutlar için bölgeler ve coğrafi sınırlar hakkında bilgi sağlar.

| **Geopolitik bölge** | **Azure bölgeleri** | **ExpressRoute konumları** |
| --- | --- | --- |
| **ABD bulutu** |US Gov Arizona, US Gov Iowa, US Gov Teksas, US Gov Virginia, Orta US DoD, Doğu US DoD  |Atlanta, Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Doğu Çin** |Doğu Çin, Doğu Çin2 |Shanghai, Shanghai2 |
| **Kuzey Çin** |Kuzey Çin, Kuzey Çin2 |Pekin, Beijing2 |
| **Almanya** |Orta Almanya, Almanya Doğu |Berlin, Frankfurt |

Coğrafi bölgeler arasındaki bağlantı standart ExpressRoute SKU’da desteklenmiyor. Genel bağlantıyı desteklemek için ExpressRoute premium eklentisini etkinleştirmeniz gerekir. Ulusal bulut ortamlarına bağlantı desteklenmiyor. Bu tür bir ihtiyaç ortaya çıkarsa bağlantı sağlayıcınız ile çalışabilirsiniz.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute bağlantı sağlayıcıları

Aşağıdaki tabloda hizmet sağlayıcısına göre konumlar gösterilmektedir. Kullanılabilir sağlayıcıları konuma göre görüntülemek istiyorsanız bkz. [Konuma göre hizmeti sağlayıcılar](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Küresel ticari Azure

| **Hizmet sağlayıcı** | **Microsoft Azure** | **Microsoft 365**  | **Konumlar** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Desteklenir |Desteklenir |Melbourne, Sidney |
| **[Airtel](https://www.airtel.in/business/#/)** | Desteklenir | Desteklenir | Madras2, Madras2 |
| **[AıS](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Desteklenir | Desteklenir | Bangkok |
| **[Aryaka Networks](https://www.aryaka.com/)** |Desteklenir |Desteklenir |Amsterdam, Chicago, Dallas, Hong Kong ÖIB, Sao Paulo, Seattle, Silicon Valley, Singapur, Tokyo, Washington DC |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Desteklenir |Desteklenir |Sao Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Desteklenir |Desteklenir |Amsterdam, Chicago, Dallas, Frankfurt, Londra, Silicon Valley, Singapur, Sidney, Tokyo, Toron, Washington DC |
| **[TOKYO 'DA](https://www.attokyo.com/connectivity/azure.html)** | Desteklenir | Desteklenir | Osaka, Tokyo2 |
| **[BBIX](https://www.bbix.net/en/service/ix/)** | Desteklenir | Desteklenir | Tokyo |
| **[BCX](https://www.bcx.co.za/solutions/connectivity/data-networks)** |Desteklenir |Desteklenir |Cape Town, Johannesburg|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Desteklenir |Desteklenir |Montreal, Toronto, Quebec City |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Desteklenir |Desteklenir |Amsterdam, Amsterdam2, Chicago, Hong Kong ÖIB, Johannesburg, Londra, London2, Newport (Wales), Paris, Sao Paulo, Silicon Valley, Singapur, Sidney, Tokyo, Washington DC |
| **[BSNL](https://www.bsnl.co.in/opencms/bsnl/BSNL/services/enterprises/cloudway.html)** |Desteklenir |Desteklenir |Chennai, Mumbai |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Desteklenir |Desteklenir |Miami |
| **CDC** | Desteklenir | Desteklenir | Kanberra, Kanberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Desteklenir |Desteklenir |Amsterdam2, Chicago, Dublin, Frankfurt, Hong Kong, Las Vegas, London2, New York, Paris, San Antonio, Silicon Valley, Tokyo, Toron, Washington DC, Washington DC2 |
| **[Baş Telem](https://www.chief.com.tw/)** |Desteklenir |Desteklenir |Hong Kong, Taipei |
| **Çin Mobile Uluslararası** |Desteklenir |Desteklenir | Hong Kong, Hong Kong2, Singapur |
| **China Telecom Global** |Desteklenir |Desteklenir |Hong Kong, Hong Kong2 |
| **Çin tek noktaya yayın küresel** |Desteklenir |Desteklenir | Hong Kong, Singapur2 |
| **[Chunghwa telekomünikasyon](https://www.cht.com.tw/en/home/cht/about-cht/products-and-services/International/Cloud-Service)** |Desteklenir |Desteklenir |Taipei |
| **[Claro](https://www.usclaro.com/enterprise-mnc/connectivity/mpls/)** |Desteklenir |Desteklenir |Miami |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Desteklenir |Desteklenir |Chicago, Dallas, Minneapolis, Montreal, Toron, Vansu ver, Washington DC |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Desteklenir |Desteklenir |Amsterdam, Amsterdam2, Berlin, Chicago, Dublin, Frankfurt, Hong Kong, Londra, London2, MILAN, Newport, New York, Osaka, Paris, Silicon Valley, Silicon Valley2, Singapur2, Tokyo, Washington DC, Zurich |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Desteklenir |Desteklenir |Chicago, Silikon Vadisi, Washington DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Desteklenir |Desteklenir |Chicago, Denver, Los Angeles, New York, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2 |
| **[DE-CıX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange/find-a-cloud-service/detail/microsoft-azure)** | Desteklenir |Desteklenir |Amsterdam2, Dubai2, Frankfurt, Marseille, Mumbai, Münih, New York |
| **[Devoli](https://devoli.com/expressroute)** | Desteklenir |Desteklenir | Auckland, Melbourne, Sidney |
| **du datamena** |Desteklenir |Desteklenir | Dubai2 |
| **eir** |Desteklenir |Desteklenir |Dublin|
| **[Epsilon genel Iletişimler](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Desteklenir |Desteklenir |Singapur, Singapur2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Desteklenir |Desteklenir |Amsterdam, Amsterdam2, Atlanta, Berlin, Bogota, Canberra2, Chicago, Dallas, Dubai2, Dublin, Frankfurt, Frankfurt2, Geneva, Hong Kong ÖIB, Londra, London2, Los Angeles, Los Angeles2, Melbourne, Miamı, MILAN, New York, Osaka, Paris, RIO, İstanbul, Pımh, Tokyo, toron |
| **Etisalat UAE** |Desteklenir |Desteklenir |Dubai|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Desteklenir |Desteklenir |Amsterdam, Amsterdam2, Dublin, Frankfurt, Londra |
| **[FarEasTone](https://www.fetnet.net/corporate/en/Enterprise.html)** |Desteklenir |Desteklenir |Taipei|
| **[Fastweb](https://www.fastweb.it/grandi-aziende/cloud/scheda-prodotto/fastcloud-interconnect/)** | Desteklenir |Desteklenir |Milano|
| **[Fibrenoire](https://fibrenoire.ca/en/services/cloudextn-2/)** |Desteklenir |Desteklenir |Montreal|
| **[GÉANT](https://www.geant.org/Networks)** |Desteklenir |Desteklenir |Amsterdam, Amsterdam2, Dublin, Frankfurt, Marseille |
| **[GlobalConnect]()** | Desteklenir |Desteklenir | Oslo, Stavanger | 
| **GTT** |Desteklenir |Desteklenir |London2 |
| **[Genel Bulut Değişimi (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Desteklenir| Desteklenir | Chennai, Mumbai |
| **[ıadvantage](https://www.scx.sunevision.com/)** | Desteklenir | Desteklenir | Hong Kong2 |
| **Intelsat** | Desteklenir | Desteklenir | Washington DC2 |
| **[InterCloud](https://www.intercloud.com/)** |Desteklenir |Desteklenir |Amsterdam, Chicago, Frankfurt, Hong Kong, Londra, New York, Paris, Silicon Valley, Singapur, Washington DC, Zurich |
| **[Internet2](https://internet2.edu/services/cloud-connect/#service-cloud-connect)** |Desteklenir |Desteklenir |Chicago, Dallas, Silicon Valley, Washington DC |
| **[İnternet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Desteklenir |Desteklenir |Osaka, Tokyo |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Desteklenir |Desteklenir |Cape Town, Johannesburg, Londra |
| **[Interxiyon](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Desteklenir |Desteklenir |Amsterdam, Amsterdam2, Copenhagen, Dublin, Frankfurt, Londra, Madrid, Marseille, Istanbul, Zurich |
| **[IRıDEOS](https://irideos.it/)** |Desteklenir |Desteklenir |Milano |
| **Iron Sıradağlar** | Desteklenir |Desteklenir |Washington DC |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Desteklenir |Desteklenir | Amsterdam, London2, Silicon Valley, Toron, Washington DC |
| **Jaguar ağı** |Desteklenir |Desteklenir |Marseille, Paris |
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Desteklenir |Desteklenir |Londra, Newport (Wales) |
| **[KINX](https://www.kinx.net/service/cloudhub/ms-expressroute/?lang=en)** |Desteklenir |Desteklenir |Seul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Desteklenir |Desteklenir |Auckland, Sidney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Desteklenir | Desteklenir | Amsterdam |
| **[PAKET](https://cloud.kt.com/)** | Desteklenir | Desteklenir | Seul |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Desteklenir |Desteklenir |Amsterdam, Chicago, Dallas, Londra, Newport (Wales), Sao Paulo, Seattle, Silicon Valley, Singapur, Washington DC |
| **LG CNS** |Desteklenir |Desteklenir |Busan, Seul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Desteklenir |Desteklenir |Cape Town, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Desteklenir |Desteklenir |Amsterdam, Atlanta, Auckland, Chennai, Chicago, Dallas, Denver, Dubai2, Dublin, Frankfurt, Geneva, Hong Kong, Hong Kong2, Las Vegas, Londra, London2, Los Angeles, Melbourne, Miamı, Minneapolis, Montreal, New York, Osaka, Oslo, Paris, Perth, Quebec City, San Antonio, Seattle, Silicon Valley, Singapur, Singapur2, Stavanger, Stockholm, Sidney, Sydney2, Tokyo, Tokyo2 Toron, Vanur ver, Washington DC, Washington DC2, Zurich |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** |Desteklenir |Desteklenir |Londra |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Desteklenir |Desteklenir |Dallas, Los Angeles, MIAMI, Sao Paulo, Washington DC |
| **[Next Generation Data](https://vantage-dc-cardiff.co.uk/)** |Desteklenir |Desteklenir |Newport(Galler) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Desteklenir |Desteklenir |Melbourne, Perth, Sidney, Sydney2 |
| **[ALANıNDA](https://www.nos.pt/empresas/corporate/cloud/cloud/Pages/nos-cloud-connect.aspx)** |Desteklenir |Desteklenir |Amsterdam2 |
| **[NTT Iletişimleri](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Desteklenir |Desteklenir |Amsterdam, Hong Kong ÖIB, Londra, Los Angeles, Osaka, Singapur, Sidney, Tokyo, Washington DC |
| **[NTT EAST](https://business.ntt-east.co.jp/service/crossconnect/)** |Desteklenir |Desteklenir |Tokyo |
| **[NTT küresel veri merkezleri EMEA](https://hello.global.ntt/)** |Desteklenir |Desteklenir |Amsterdam2, Berlin |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Desteklenir |Desteklenir |Osaka |
| **[Ooredoo Cloud Connect](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |Desteklenir |Desteklenir |Marsilya |
| **[Optus](https://www.optus.com.au/enterprise/)** |Desteklenir |Desteklenir |Melbourne, Sidney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Desteklenir |Desteklenir |Amsterdam, Amsterdam2, Dubai2, Frankfurt, Hong Kong ÖIB, Johannesburg, Londra, Paris, Sao Paulo, Silicon Valley, Singapur, Sidney, Tokyo, Washington DC |
| **[Cumxcom](https://www.orixcom.com/cloud-solutions/)** | Desteklenir | Desteklenir | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Desteklenir |Desteklenir |Chicago, Dallas, Las Vegas, Silicon Valley, Washington DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Desteklenir |Desteklenir |Chicago, Hong Kong, Hong Kong2, Londra, Singapur2 |
| **[REANNZ](https://www.reannz.co.nz/products-and-services/cloud-connect/)** | Desteklenir | Desteklenir | Auckland |
| **[Yeniden temellendirme](https://www.retelit.it/EN/Home.aspx)** | Desteklenir | Desteklenir | Milano | 
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Desteklenir |Desteklenir |Seul |
| **[IR](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Desteklenir |Desteklenir | London2, Washington DC |
| **[SıFY](http://telecom.sify.com/azure-expressroute.html)** |Desteklenir |Desteklenir |Madras, Bombay2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Desteklenir |Desteklenir |Hong Kong2, Singapur, Singapur2 |
| **[Yazılım Bankası](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Desteklenir |Desteklenir |Osaka, Tokyo |
| **[Sohonet](https://www.sohonet.com/fastlane/)** |Desteklenir |Desteklenir |London2 |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Desteklenir |Desteklenir |Auckland, Sidney |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Desteklenir |Desteklenir |Chicago, Silikon Vadisi, Washington DC |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Desteklenir | Desteklenir | Geneva, Zurich |
| **[Tata Communications](https://www.tatacommunications.com/solutions/network/cloud-ready-networks/)** |Desteklenir |Desteklenir |Amsterdam, Chennai, Hong Kong ÖIB, Londra, Mumbai, Sao Paulo, Silicon Valley, Singapur, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Desteklenir |Desteklenir |Amsterdam, Sao Paulo |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Desteklenir |Desteklenir |Londra, London2, Singapur2 |
| **Telenor** |Desteklenir |Desteklenir |Amsterdam, Londra, Oslo |
| **[Telia Carrier](https://www.teliacarrier.com/)** | Desteklenir | Desteklenir |Amsterdam, Chicago, Dallas, Frankfurt, Hong Kong, Londra, Oslo, Paris, Silicon Valley, Stockholm, Washington DC |
| **[Telın](https://www.telin.net/product/data-connectivity/telin-cloud-exchange)** | Desteklenir | Desteklenir |Cakarta |
| **Telmex Uninet**| Desteklenir | Desteklenir | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Desteklenir |Desteklenir |Melbourne, Singapur, Sidney |
| **[Telus](https://www.telus.com)** |Desteklenir |Desteklenir |Montreal, Seattle, Quebec City, Torüzerine, Vansu ver |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Desteklenir |Desteklenir |Cape Town, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/direct-cloud)** | Desteklenir | Desteklenir | Kuala Lumpur |
| **[Tokai Iletişimleri](https://www.tokai-com.co.jp/en/)** | Desteklenir | Desteklenir | Osaka, Tokyo2 |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Desteklenir |Desteklenir |Dallas, Queretaro (Meksika)|
| **T-Systems** |Desteklenir |Desteklenir |Frankfurt|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Desteklenir |Desteklenir |Sao Paulo |
| **[UıH](https://www.uih.co.th/en/network-solutions/global-network/cloud-direct-for-microsoft-azure-expressroute)** | Desteklenir | Desteklenir | Bangkok |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Desteklenir |Desteklenir |Amsterdam, Chicago, Dallas, Hong Kong ÖIB, Londra, Mumbai, Silicon Valley, Singapur, Sidney, Tokyo, Toron, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Desteklenir | Desteklenir | Washington DC2 |
| **[Vocus grubu NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Desteklenir | Desteklenir | Auckland, Sidney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Desteklenir |Desteklenir |Amsterdam2, Londra, Singapur |
| **[Vodadfone fikri](https://www.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Desteklenir | Desteklenir | Bombay2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Desteklenir |Desteklenir |Amsterdam, Chicago, Dallas, Denver, Londra, Los Angeles, Montreal, New York, Paris, Seattle, Silicon Valley, Toron, Washington DC, Washington DC2 |

 **+** çok yakında geliyor

### <a name="national-cloud-environment"></a>Ulusal bulut ortamı

Azure Ulusal bulutlar birbirinden ve küresel ticari Azure 'dan yalıtılmıştır. Bir Azure bulutu için ExpressRoute, diğer Azure bölgelerine bağlanamaz. 

### <a name="us-government-cloud"></a>ABD bulutu

| **Hizmet sağlayıcı** | **Microsoft Azure** | **Office 365** | **Konumlar** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Desteklenir |Desteklenir |Chicago, Phoenix, Silicon Valley, Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Desteklenir |Desteklenir |New York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Desteklenir |Desteklenir |Atlanta, Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Desteklenir |Desteklenir |Chicago, Silikon Vadisi, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Desteklenir | Desteklenir | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Desteklenir |Desteklenir |Chicago, Dallas, New York, Silikon Vadisi, Washington DC |

### <a name="china"></a>Çin

| **Hizmet sağlayıcı** | **Microsoft Azure** | **Office 365** | **Konumlar** |
| --- | --- | --- | --- |
| **China Telecom** |Desteklenir |Desteklenmiyor |Pekin, Beijing2, Shanghai, Shanghai2 |
| **Çin Unicom** | Desteklenir | Desteklenmiyor | Beijing2, Shanghai2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |Desteklenir |Desteklenmiyor |Beijing2, Shanghai2 |

Daha fazla öğrenmek için, bkz. [Çin’de ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Almanya

| **Hizmet sağlayıcı** | **Microsoft Azure** | **Office 365** | **Konumlar** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Desteklenir |Desteklenmiyor |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Desteklenir |Desteklenmiyor |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroute)** |Desteklenir |Desteklenmiyor |Berlin |
| **Interxiyon** |Desteklenir |Desteklenmiyor |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Desteklenir  | Desteklenmiyor | Berlin |
| **T-Systems** |Desteklenir |Desteklenmiyor |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Exchange sağlayıcıları üzerinden bağlantı

Bağlantı sağlayıcınız önceki bölümlerde listelenmemişse hala bağlantı oluşturabilirsiniz.

* Yukarıdaki tabloda yer alan değişimlerin herhangi birine bağlı olup olmadığını görmek için bağlantı sağlayıcınıza başvurun. Değişim sağlayıcıları tarafından sunulan hizmetler hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kontrol edebilirsiniz. Birkaç bağlantı sağlayıcı Ethernet değişimlerine zaten bağlı.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CıX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxiyon](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
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

## <a name="connectivity-through-additional-service-providers"></a>Ek hizmet sağlayıcıları üzerinden bağlantı

| **Bağlantı sağlayıcısı** | **cihazlar** | **Konumlar** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** | Equinix |Singapur |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/Business)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokyo |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/business/cloud-exchange)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londra |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, Londra, Singapur, Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokyo |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/)** | Equinix | Hong Kong ÖİB |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[CMC telekomünikasyon](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapur | 
| **[Aptum teknolojileri](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](https://www.coreazure.com/)**| Equinix | Londra |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Silikon Vadisi, Washington DC |
| **[Crown kalele](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, New York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/solutions/cloud-connect/)** | Equinix | Londra, Singapur, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londra |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Quebec City |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Köprüsü Uluslararası](https://gbiinc.com/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londra, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam telekomünikasyon Bahreyn B. S. C](http://www.kalaam-telecom.com/azure/)**| Level 3 Communications |Amsterdam |
| **LGA Telecom** |Equinix |Singapur|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hong Kong ÖİB 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sidney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** | Teraco | Cape Town, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londra |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Frankfurt |
| **[Oncore bulut hizmeti Inc](https://www.oncore.cloud/services/ue-for-expressroute)**| Equinix | Toronto |
| **[Telekom SONRASı Lüksemburg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, Londra, Paris |
| **[QSC AG](https://www2.qbeyond.de/en/)** |Interxion | Frankfurt |  
| **[RETN](https://retn.net/services/cloud-connect/)** | Equinix | Amsterdam |
| **[Tata TeleServices](https://www.tatateleservices.com/business-services/data-services/secure-cloud-connect)** | Tata Communications | Chennai, Mumbai |
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[SPIN kurumsal](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Silicon Valley | 
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | Londra | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telekoa Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapur |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New Yok |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silikon Vadisi, Washington DC |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon Valley, Silicon Valley 2|
| **Zain** |Equinix |Londra|
| **[Zertia](https://www.zertia.es)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Veri merkezi sağlayıcıları üzerinden bağlantı

| **Sağlayıcı** | **cihazlar** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[Veri Dökümiz](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | X REACH, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Esnek](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | X REACH, Megaport, PacketFabric |
| **[QTS veri merkezleri](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Akış veri merkezleri]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | X REACH, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | X REACH, Megaport |
| **[T5 Datacenters](https://t5datacenters.com/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Ulusal Araştırma ve eğitim ağları (NREN) üzerinden bağlantı

| **Sağlayıcı**|
| --- |
| **AARNET**| 
| **GÉANT aracılığıyla DeIC**|
| **GÉANT aracılığıyla GARR**|
| **GÉANT**|
| **GÉANT aracılığıyla HEAnet**|
| **Internet2**|
| **JıSC**|
| **GÉANT aracılığıyla RedIRIS**|
| **SINET**|
| **GÉANT aracılığıyla Surfnet**|

* Bağlantı sağlayıcınız bu listede yoksa, lütfen yukarıda listelenen ExpressRoute Exchange İş Ortaklarından herhangi birine bağlı olup olmadığınızı denetleyin.

## <a name="expressroute-system-integrators"></a>ExpressRoute sistem tümleştiricileri
İhtiyaçlarınıza uyan özel bağlantıyı etkinleştirme ağınızın ölçeğine bağlı olarak zorlu olabilir. ExpressRoute’a yönelik ekleme işleminde size yardımcı olmak üzere aşağıdaki tabloda listelenen herhangi bir sistem tümleştirici ile çalışabilirsiniz.

| **Sistem tümleştirici** | **Kıta** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Avrupa |
| **[Avanade Inc.](https://www.avanade.com/)** | Asya, Avrupa, Kuzey Amerika, Güney Amerika |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Avrupa
| **[Ensyst](https://www.ensyst.com.au)** | Asya
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Kuzey Amerika |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Kuzey Amerika |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Kuzey Amerika |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Avustralya |
| **[MOQdigital](https://www.moqdigital.com/insights)** | Avustralya |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Avrupa (Almanya) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Avrupa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Avrupa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Asya |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Avrupa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Kuzey Amerika |
| **[Presidio](https://www.presidio.com/subpage/1107/microsoft-azure)** | Kuzey Amerika |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Avrupa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Güney Amerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Avustralya |

## <a name="next-steps"></a>Sonraki adımlar
* ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).
* Tüm önkoşulların sağlandığından emin olun. Bkz. [ExpressRoute önkoşulları](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Konum eşleme"
