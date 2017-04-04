---
title: "Bağlantı sağlayıcıları ve konumları: Azure ExpressRoute | Microsoft Docs"
description: "Bu makale, sunulan hizmetlerin konumları ve Azure bölgelerine nasıl bağlanılacağı hakkında ayrıntılı bir genel bakış sağlar. Bağlantı sağlayıcısına göre sıralanır."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 70955113bbd2e008be855e591d04651d09438e81
ms.lasthandoff: 03/24/2017


---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute ortakları ve eşleme konumları

> [!div class="op_single_selector"]
> * [Sağlayıcıya Göre Konumlar](expressroute-locations.md)
> * [Konuma Göre Sağlayıcılar](expressroute-locations-providers.md)


Bu makaledeki tablolar ExpressRoute bağlantı sağlayıcıları, ExpressRoute coğrafi kapsamı, ExpressRoute üzerinden desteklenen Microsoft bulut hizmetleri ve ExpressRoute Sistem Tümleştiricileri (SIs) hakkında bilgi sağlar.

## <a name="partners"></a>ExpressRoute bağlantı sağlayıcıları
ExpressRoute tüm Azure bölgeleri ve konumları arasında desteklenir. Aşağıdaki harita Azure bölgeleri ve ExpressRoute konumlarının listesini sağlar. ExpressRoute konumları birkaç hizmet sağlayıcının sahip olduğu Microsoft eşlerine başvurur.

![Konum eşleme][0]

Coğrafi bölge içindeki en az bir ExpressRoute konumuna bağlanırsanız coğrafi bölge içindeki tüm bölgeler arasında Azure hizmetlerine erişebileceksiniz.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Bir coğrafi bölge içindeki Azure bölgeler ile ExpressRoute konumları arasında eşleme.
Aşağıdaki tablo, coğrafi bölge içindeki Azure bölgeler ile ExpressRoute konumları arasında yapılan eşlemeyi sağlar.

| **Jeopolitik bölge** | **Azure bölgeleri** | **ExpressRoute konumları** |
| --- | --- | --- |
| **Kuzey Amerika** |Doğu ABD, Batı ABD, Doğu ABD 2, Batı ABD 2, Orta ABD, Orta Güney ABD, Orta Kuzey ABD, Batı Orta ABD, Orta Kanada, Doğu Kanada |Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, New York, Seattle, Silikon Vadisi, Washington DC, Montreal, Quebec City, Toronto |
| **Güney Amerika** |Güney Brezilya |Sao Paulo |
| **Avrupa** |Kuzey Avrupa, Batı Avrupa, İngiltere Batı, İngiltere Güney |Amsterdam, Dublin, Londra, Newport(Galler), Paris |
| **Asya** |Doğu Asya, Güneydoğu Asya |Hong Kong, Singapur |
| **Japonya** |Batı Japonya, Doğu Japonya |Osaka, Tokyo |
| **Avustralya** |Güneydoğu Avustralya, Doğu Avustralya |Melbourne, Sidney |
| **Hindistan** |Batı Hindistan, Orta Hindistan, Güney Hindistan |Chennai, Mumbai |
| **Güney Kore** |Kore Orta, Kore Güney |Busan, Seul |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Ulusal bulutlar için bölgeler ve coğrafi sınırlar
Aşağıdaki tablo ulusal bulutlar için bölgeler ve coğrafi sınırlar hakkında bilgi sağlar.

| **Jeopolitik bölge** | **Azure bölgeleri** | **ExpressRoute konumları** |
| --- | --- | --- | --- |
| **US Government bulutu** |ABD Devleti Iowa, ABD Devleti Virginia, US DoD Orta, US DoD Doğu  |Chicago, Dallas, New York, Silikon Vadisi, Washington DC |
| **Çin** |Kuzey Çin, Doğu Çin |Pekin, Şangay |
| **Almanya** |Orta Almanya, Doğu Almanya |Berlin, Frankfurt |

Coğrafi bölgeler arasındaki bağlantı standart ExpressRoute SKU’da desteklenmiyor. Genel bağlantıyı desteklemek için ExpressRoute premium eklentisini etkinleştirmeniz gerekir. Ulusal bulut ortamlarına bağlantı desteklenmiyor. Bu tür bir ihtiyaç ortaya çıkarsa bağlantı sağlayıcınız ile çalışabilirsiniz.

## <a name="locations"></a>Bağlantı sağlayıcı konumları

Aşağıdaki tabloda hizmet sağlayıcısına göre konumlar gösterilmektedir. Kullanılabilir sağlayıcıları konuma göre görüntülemek istiyorsanız bkz. [Konuma göre hizmeti sağlayıcılar](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Üretim Azure
| **Hizmet sağlayıcı** | **Microsoft Azure** | **Office 365 ve CRM Online** | **Konumlar** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Destekleniyor |Destekleniyor |Melbourne, Sidney |
| **Airtel** | Çok Yakında | Çok Yakında | Chennai, Mumbai |
| **[Aryaka Networks](http://www.aryaka.com/)** |Destekleniyor |Destekleniyor |Amsterdam, Dallas, Silikon Vadisi, Singapur, Tokyo, Washington DC |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Destekleniyor |Destekleniyor |Amsterdam, Chicago, Dallas, Londra, Silikon Vadisi, Singapur, Sidney, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Destekleniyor |Destekleniyor |Montreal, Toronto |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |Destekleniyor |Destekleniyor |Amsterdam, Hong Kong, Londra, Silikon Vadisi, Singapur, Sidney, Tokyo, Washington DC |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |Çok yakında |Çok yakında |Silikon Vadisi |
| **China Telecom Global** |Destekleniyor |Desteklenmiyor |Hong Kong |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Destekleniyor |Destekleniyor |Dallas, Montreal, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Destekleniyor |Destekleniyor |Amsterdam, Dublin, Londra, Tokyo |
| **Comcast** |Destekleniyor |Destekleniyor |Chicago, Silikon Vadisi, Washington DC |
| **Console**| Destekleniyor | Destekleniyor |Silicon Valley, Toronto |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Destekleniyor |Destekleniyor |Los Angeles, New York |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Destekleniyor |Destekleniyor |Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londra, Los Angeles, Melbourne, New York, Osaka, Paris+, Sao Paulo, Seattle, Silikon Vadisi, Singapur, Sidney, Tokyo, Toronto, Washington DC |
| **euNetworks** |Destekleniyor |Destekleniyor |Amsterdam |
| **[Global Cloud Xchange (GCX)] (http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/cloud-x-fusion-for-azure/)** | Destekleniyor| Destekleniyor | Chennai |
| **GÉANT** |Destekleniyor |Destekleniyor |Amsterdam |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Destekleniyor |Destekleniyor |Osaka, Tokyo |
| **[InterCloud](https://www.intercloud.com/)** |Destekleniyor |Destekleniyor |Amsterdam, Londra, Singapur, Washington DC |
| **Internet Solutions - Cloud Connect** |Destekleniyor |Destekleniyor |Amsterdam, Londra |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Destekleniyor |Destekleniyor |Amsterdam, Londra, Paris |
| **Jisc** |Destekleniyor |Destekleniyor |Londra |
| **KINX** |Destekleniyor |Destekleniyor |Seul |
| **[KPN](http://www.kpn.com/cloudconnect)** | Destekleniyor | Destekleniyor | Amsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Destekleniyor |Destekleniyor |Amsterdam, Chicago, Dallas, Las Vegas+, Londra, Seattle, Silikon Vadisi, Singapur, Washington DC |
| **LG CNS** |Çok Yakında |Çok Yakında |Busan+ |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Destekleniyor |Destekleniyor |Dallas, Hong Kong, Las Vegas, Los Angeles, Melbourne, New York, Quebec City, Seattle, Singapur, Sidney, Toronto, Washington DC |
| **MTN** |Destekleniyor |Destekleniyor |Londra |
| **[Next Generation Data](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Destekleniyor |Destekleniyor |Newport(Galler) |
| **NEXTDC** |Destekleniyor |Destekleniyor |Melbourne, Sidney |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |Destekleniyor |Destekleniyor |Londra, Los Angeles, Osaka, Singapur, Tokyo, Washington DC |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Destekleniyor |Destekleniyor |Amsterdam, Hong Kong, Londra, Silikon Vadisi, Singapur, Sidney, Washington DC |
| **PCCW Global Limited** |Destekleniyor |Destekleniyor |Hong Kong |
| **Sejong Telecom** |Destekleniyor |Destekleniyor |Seul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Destekleniyor |Destekleniyor |Chennai |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Destekleniyor |Destekleniyor |Singapur |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Destekleniyor |Destekleniyor |Osaka, Tokyo |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Destekleniyor |Destekleniyor |Amsterdam, Chennai, Hong Kong, Londra, Mumbai, Silikon Vadisi, Singapur, Washington DC |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |Destekleniyor |Destekleniyor |Amsterdam, Dublin, Londra |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Destekleniyor |Destekleniyor |Amsterdam+, Sao Paulo |
| **[Telehouse - KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |Destekleniyor |Destekleniyor |Londra |
| **Telenor** |Destekleniyor |Destekleniyor |Amsterdam, Londra |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Destekleniyor |Destekleniyor |Melbourne, Sidney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Destekleniyor |Destekleniyor |Amsterdam, Chicago, Dallas, Hong Kong, Londra, Silikon Vadisi, Singapur, Sidney, Tokyo, Washington DC |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Destekleniyor |Desteklenmiyor |Londra |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Destekleniyor |Destekleniyor |Chicago, Dallas+, London+, Los Angeles, New York, Silikon Vadisi, Toronto, Washington DC |

 **+** çok yakında anlamına geliyor

### <a name="national-cloud-environment"></a>Ulusal bulut ortamı

### <a name="us-government-cloud"></a>US Government bulutu
| **Hizmet sağlayıcı** | **Microsoft Azure** | **Office 365** | **Konumlar** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Destekleniyor |Destekleniyor |Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Destekleniyor |Destekleniyor |Chicago, Dallas, New York, Seattle+, Silikon Vadisi, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Destekleniyor |Destekleniyor |Chicago, New York+, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Destekleniyor | Destekleniyor | Dallas |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Destekleniyor |Destekleniyor |Chicago, Dallas, New York, Washington DC |

### <a name="china"></a>Çin
| **Hizmet sağlayıcı** | **Microsoft Azure** | **Office 365** | **Konumlar** |
| --- | --- | --- | --- |
| **China Telecom** |Destekleniyor |Desteklenmiyor |Pekin, Şangay |

Daha fazla öğrenmek için, bkz. [Çin’de ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Almanya
| **Hizmet sağlayıcı** | **Microsoft Azure** | **Office 365** | **Konumlar** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Destekleniyor |Desteklenmiyor |Berlin+, Frankfurt |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Destekleniyor |Desteklenmiyor |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Destekleniyor |Desteklenmiyor |Berlin |
| **Interxion** |Destekleniyor |Desteklenmiyor |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Destekleniyor  | Desteklenmiyor | Berlin |

## <a name="c1partners"></a>Listelenmeyen hizmet sağlayıcılar üzerinden bağlantı
Bağlantı sağlayıcınız önceki bölümlerde listelenmemişse hala bağlantı oluşturabilirsiniz.

* Yukarıdaki tabloda yer alan değişimlerin herhangi birine bağlı olup olmadığını görmek için bağlantı sağlayıcınıza başvurun. Değişim sağlayıcıları tarafından sunulan hizmetler hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kontrol edebilirsiniz. Birkaç bağlantı sağlayıcı Ethernet değişimlerine zaten bağlı.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Bağlantı sağlayıcınızı, ağınızı seçtiğiniz eşleme konumuna genişletmesini sağlayın.
  * Bağlantı sağlayıcınızın bağlantınızı yüksek oranda kullanılabilir şekilde genişlettiğinden emin olun, böylece hiç tek nokta arızası olmaz.
* Microsoft’a bağlanmak için bağlantı sağlayınız olarak değişime sahip bir ExpressRoute bağlantı hattı sipariş edin.
  * Bağlantı kurmak için [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-classic.md)’daki adımları izleyin.

| **Bağlantı sağlayıcı** | **Exchange** | **Konumlar** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapur |
| **[Airgate Technologies, Inc.](http://airgate.ca/cloud-express/)** | Equinix, Cologix | Toronto, Montreal |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |Tokyo |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londra |
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montreal, Toronto |
| **[C3ntro](http://www.c3ntro.com/data/cloud-conectivity/)** | Equinix, Megaport | Dallas |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Singapur |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londra |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | Londra, Slough |
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix |New York, Washington DC |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sidney |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion  London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Frankfurt |  
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | Londra
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix |Chicago, Silikon Vadisi, Washington DC |
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | Madrid |


## <a name="expressroute-system-integrators"></a>ExpressRoute sistem tümleştiricileri
İhtiyaçlarınıza uyan özel bağlantıyı etkinleştirme ağınızın ölçeğine bağlı olarak zorlu olabilir. ExpressRoute’a yönelik ekleme işleminde size yardımcı olmak üzere aşağıdaki tabloda listelenen herhangi bir sistem tümleştirici ile çalışabilirsiniz.

| **Sistem tümleştirici** | **Continent** |
| --- | --- |
| **[Altogee](http://www.altogee.be/expressroute)** | Avrupa |
| **[Avanade Inc.](http://www.avanade.com/)** | Asya, Avrupa, Kuzey Amerika, Güney Amerika |
| **Bright Skies GmbH** | Avrupa
| **[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)** | Avrupa |
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | Kuzey Amerika |
| **[The IT Consultancy Group](http://itconsult.com.au/microsoft-expressroute)** | Avustralya |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Avrupa (Almanya) |
| **[Nelite](http://nelite.com/)** | Avrupa |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Asya |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Avrupa |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Kuzey Amerika |
| **[Project Leadership](http://www.projectleadership.net/azure)** | Kuzey Amerika |
| **[sol-tec](http://www.sol-tec.com/Technologies)** | Avrupa |


## <a name="next-steps"></a>Sonraki adımlar
* ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).
* Tüm önkoşulların sağlandığından emin olun. Bkz. [ExpressRoute önkoşulları](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Konum eşleme"

