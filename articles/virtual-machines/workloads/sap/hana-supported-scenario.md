---
title: Azure 'da SAP HANA için desteklenen senaryolar (büyük örnekler) | Microsoft Docs
description: Desteklenen senaryolar ve Azure 'daki SAP HANA için mimari ayrıntıları (büyük örnekler)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a49c55da0bb8018b3faa7c6f70b02e79d9478603
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666691"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA büyük örnekleri için desteklenen senaryolar
Bu makalede, HANA büyük örnekler (HLI) için desteklenen senaryolar ve mimari ayrıntıları açıklanmaktadır.

>[!NOTE]
>Gerekli senaryonuz bu makalede bahsedilmez, gereksinimlerinizi değerlendirmek için Microsoft hizmet yönetimi ekibine başvurun.
HLI birimini ayarlamadan önce SAP veya hizmet uygulama iş ortağınızdan tasarımı doğrulayın.

## <a name="terms-and-definitions"></a>Terimler ve tanımlar
Bu makalede kullanılan hüküm ve tanımları anlayalim:

- **SID**: Hana sistemi için bir sistem tanımlayıcısı
- **HLi**: Hana büyük örnekleri
- **Dr**: olağanüstü durum kurtarma
- **Normal Dr**: yalnızca Dr amaçları için ayrılmış kaynağa sahip bir sistem kurulumu
- **Çok AMAÇLı Dr**: bir Dr olayı için yapılandırılmış bir üretim örneği ile birlikte, üretim dışı bir ortamı kullanacak şekilde YAPıLANDıRıLMıŞ bir Dr-site sistemi 
- **Tek SID**: bir örneği yüklü bir sistem
- **Çoklu SID**: birden çok örneği yapılandırılmış bir sistem; MCOS ortamı da denir
- **HSR**: sistem çoğaltmasını SAP HANA

## <a name="overview"></a>Genel Bakış
HANA büyük örnekleri, iş gereksinimlerinizi gerçekleştirmenize yardımcı olmak için çeşitli mimarilerin kullanılmasını destekler. Aşağıdaki bölümlerde mimari senaryolar ve bunların yapılandırma ayrıntıları ele alınmaktadır. 

Türetilmiş mimari tasarımı yalnızca bir altyapı perspektifinden, SAP veya HANA dağıtımı için uygulama iş ortaklarınıza danışmanız gerekir. Senaryolarınız Bu makalede listelenmiyorsa, mimariyi gözden geçirmek ve sizin için bir çözüm türetmek üzere Microsoft hesabı ekibine başvurun.

> [!NOTE]
> Bu mimariler, özel veri tümleştirme (TDı) tasarımı ile tam uyumludur ve SAP tarafından desteklenir.

Bu makalede, desteklenen her mimarideki iki bileşenin ayrıntıları açıklanmaktadır:

- Ethernet
- Depolama

### <a name="ethernet"></a>Ethernet

Sağlanan her sunucu, Ethernet arabirimleri kümeleriyle önceden yapılandırılmış olarak gelir. Her bir HLI birimde yapılandırılan Ethernet arabirimleri dört tür olarak kategorilere ayrılır:

- **A**: istemci erişimi için veya tarafından kullanılır.
- **B**: düğümden düğüme iletişim için kullanılır. Bu arabirim, istenen topolojiden bağımsız olarak tüm sunucularda yapılandırılır, ancak yalnızca genişleme senaryolarında kullanılır.
- **C**: düğümden depolamaya bağlantı için kullanılır.
- **D**: STONITH kurulumu için düğüm-iSCSI cihaz bağlantısı için kullanılır. Bu arabirim yalnızca bir HSR kurulumu istendiğinde yapılandırılır.  

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Düğümden düğüme|
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | STONıTH |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Düğümden düğüme|
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | STONıTH |

Ara birim üzerinde yapılandırılan topolojiye göre arabirimi seçersiniz. Örneğin, "B" arabirimi düğümden düğüme iletişim için ayarlanır ve bu, yapılandırılmış bir genişleme topolojisi olduğunda faydalıdır. Bu arabirim, tek düğüm, ölçek yapılandırması için kullanılmaz. Arabirim kullanımı hakkında daha fazla bilgi için, gerekli senaryolarınızı gözden geçirin (Bu makalenin ilerleyen kısımlarında). 

Gerekirse, sizin için Ek NIC kartları tanımlayabilirsiniz. Ancak, *mevcut NIC 'lerin yapılandırması değiştirilemez.*

>[!NOTE]
>Fiziksel arabirimler veya bonus olan ek arabirimler bulabilirsiniz. Kullanım durumu için yalnızca daha önce bahsedilen arabirimleri göz önünde bulundurmanız gerekir. Diğerleri yok sayılabilir.

İki atanmış IP adresine sahip birimler için dağıtım şöyle görünmelidir:

- Ethernet "A", Microsoft 'a gönderdiğiniz sunucu IP havuzu adres aralığı içinde atanmış bir IP adresine sahip olmalıdır. Bu IP adresi, işletim sisteminin */etc/hosts* dizininde tutulmalıdır.

- Ethernet "C", NFS iletişimi için kullanılan bir atanmış IP adresine sahip olmalıdır. Bu adresin, kiracı içinde örnek trafiğe izin vermek için *etc/hosts* dizininde *tutulması gerekmez.*

HANA sistem çoğaltması veya HANA genişleme dağıtımı için, iki atanmış IP adresine sahip bir dikey pencere yapılandırması uygun değildir. Yalnızca iki atanmış IP adresiniz varsa ve böyle bir yapılandırma dağıtmak istiyorsanız, Azure hizmet yönetimi 'nde SAP HANA başvurun. Üçüncü bir VLAN 'da size üçüncü bir IP adresi atayabilirler. Üç NIC bağlantı noktasında üç atanan IP adresi olan HANA büyük örnek birimleri için aşağıdaki kullanım kuralları geçerlidir:

- Ethernet "A", Microsoft 'a gönderdiğiniz sunucu IP havuzu adres aralığının dışında atanmış bir IP adresine sahip olmalıdır. Bu IP adresi, işletim sisteminin *vs/hosts* dizininde tutulmamalıdır.

- Ethernet "B", çeşitli örnekler arasında iletişim için özel olarak, *etc/hosts* dizininde tutulmalıdır. Bunlar, HANA 'nın düğümler arası yapılandırma için kullandığı IP adresleri olarak genişleme HANA yapılandırmalarında korunacak IP adresleridir.

- Ethernet "C", NFS depolama iletişimi için kullanılan bir atanmış IP adresine sahip olmalıdır. Bu tür bir adres, *etc/hosts* dizininde tutulmamalıdır.

- "D", pacemaker için yalnızca STONITH cihazlarına erişim için kullanılmalıdır. Bu arabirim, HANA sistem çoğaltmasını yapılandırırken ve SBD tabanlı bir cihaz kullanarak işletim sisteminin otomatik olarak yük devretmesini sağlamak istediğinizde gereklidir.


### <a name="storage"></a>Depolama
Depolama, istenen topolojiye göre önceden yapılandırılmıştır. Birim boyutları ve bağlama noktaları, sunucu sayısına, SKU sayısına ve yapılandırılan topolojiye bağlı olarak değişir. Daha fazla bilgi için, gerekli senaryolarınızı gözden geçirin (Bu makalenin ilerleyen kısımlarında). Daha fazla depolama gerekliyse, bunu 1 TB 'lik artışlarla satın alabilirsiniz.

>[!NOTE]
>Bağlama noktası/usr/SAP/, \<SID> /Hana/Shared bağlama noktasına yönelik sembolik bir bağlantıdır.


## <a name="supported-scenarios"></a>Desteklenen senaryolar

Sonraki bölümlerde bulunan mimari diyagramları aşağıdaki gösterimleri kullanır:

[![Mimari diyagramları tablosu](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Desteklenen senaryolar şunlardır:

* Tek bir SID içeren tek düğüm
* Tek düğümlü MCOS
* DR ile tek düğüm (normal)
* DR (çok amaçlı) ile tek düğüm
* STONITH ile HSR
* DR ile HSR (normal/çok amaçlı) 
* Konak otomatik yük devretme (1 + 1) 
* Bekleme ile genişleme
* Bekleme olmadan genişleme
* DR ile ölçeklendirme

## <a name="single-node-with-one-sid"></a>Tek bir SID içeren tek düğüm

Bu topoloji, bir SID ile genişleme yapılandırmasındaki bir düğümü destekler.

### <a name="architecture-diagram"></a>Mimari diyagramı  

![Tek bir SID içeren tek düğüm](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|/hana/shared/SID | HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Günlükleri Yinele |

### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.

## <a name="single-node-mcos"></a>Tek düğümlü MCOS

Bu topoloji, birden çok SID içeren bir ölçek yapılandırmasındaki bir düğümü destekler.

### <a name="architecture-diagram"></a>Mimari diyagramı  

![Tek düğümlü MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|/Hana/Shared/SID1 | SID1 için HANA yüklemesi | 
|/Hana/Data/SID1/mnt00001 | SID1 için veri dosyaları yüklemesi | 
|/Hana/log/SID1/mnt00001 | SID1 için günlük dosyaları yüklemesi | 
|/Hana/logbackups/SID1 | SID1 için günlükleri Yinele |
|/Hana/Shared/SID2 | SID2 için HANA yüklemesi | 
|/Hana/Data/SID2/mnt00001 | SID2 için veri dosyaları yüklemesi | 
|/Hana/log/SID2/mnt00001 | SID2 için günlük dosyaları yüklemesi | 
|/Hana/logbackups/SID2 | SID2 için günlükleri Yinele |

### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- Birim boyutu dağıtımı, bellekteki veritabanı boyutunu temel alır. Birden çok SID ortamında bellekteki veritabanı boyutlarının desteklendiğini öğrenmek için bkz. [genel bakış ve mimari](./hana-overview-architecture.md).

## <a name="single-node-with-dr-using-storage-replication"></a>Depolama çoğaltması kullanan DR ile tek düğüm
 
Bu topoloji, bir veya birden fazla SID içeren bir genişleme yapılandırmasındaki bir düğümü, birincil SID için DR sitesine depolama tabanlı çoğaltma ile destekler. Diyagramda, birincil sitede yalnızca bir tek SID sistemi gösterilmiştir, ancak MCOS sistemleri de desteklenir.

### <a name="architecture-diagram"></a>Mimari diyagramı  

![Depolama çoğaltması kullanan DR ile tek düğüm](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|/hana/shared/SID | SID için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | SID için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | SID için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | SID için günlükleri Yinele |


### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- MCOS için: birim boyutu dağıtımı, bellekteki veritabanı boyutunu temel alır. Birden çok SID ortamında bellekteki veritabanı boyutlarının desteklendiğini öğrenmek için bkz. [genel bakış ve mimari](./hana-overview-architecture.md).
- DR sitesinde: birimler ve bağlama noktaları ("HANA yüklemesi için gereklidir" olarak işaretlenir), DR HLI biriminde üretim HANA örneği yüklemesi için yapılandırılır. 
- DR sitesinde: veri, günlük yedeklemeleri ve paylaşılan birimler ("depolama çoğaltması" olarak işaretlenir), üretim sitesinden anlık görüntü aracılığıyla çoğaltılır. Bu birimler yalnızca yük devretme sırasında bağlanır. Daha fazla bilgi için bkz. [olağanüstü durum kurtarma yük devretme yordamı](./hana-overview-high-availability-disaster-recovery.md).
- *SKU türü ı sınıfı* için önyükleme birimi Dr düğümüne çoğaltılır.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Depolama çoğaltması kullanarak DR (çok amaçlı) ile tek düğüm
 
Bu topoloji, bir veya birden fazla SID içeren bir genişleme yapılandırmasındaki bir düğümü, birincil SID için DR sitesine depolama tabanlı çoğaltma ile destekler. Diyagramda, birincil sitede yalnızca bir tek SID sistemi gösterilmiştir, ancak çoklu SID (MCOS) sistemleri de desteklenir. DR sitesinde, birincil siteden üretim işlemleri çalışırken QA örneği için HLı birimi kullanılır. DR üzerinde yük devretme (veya yük devretme testi) sırasında, DR sitesindeki QA örneği alınır.

### <a name="architecture-diagram"></a>Mimari diyagramı  

![Depolama çoğaltması kullanarak DR (çok amaçlı) ile tek düğüm](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|**Birincil sitede**|
|/hana/shared/SID | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |
|**DR sitesinde**|
|/hana/shared/SID | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/shared/QA-SID | QA SID için HANA yüklemesi | 
|/Hana/Data/qa-SID/mnt00001 | QA SID için veri dosyaları yüklemesi | 
|/Hana/log/qa-SID/mnt00001 | QA SID için günlük dosyaları yüklemesi |
|/hana/logbackups/QA-SID | QA SID için günlükleri Yinele |

### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- MCOS için: birim boyutu dağıtımı, bellekteki veritabanı boyutunu temel alır. Birden çok SID ortamında bellekteki veritabanı boyutlarının desteklendiğini öğrenmek için bkz. [genel bakış ve mimari](./hana-overview-architecture.md).
- DR sitesinde: birimler ve bağlama noktaları ("HANA yüklemesi için gereklidir" olarak işaretlenir), DR HLI biriminde üretim HANA örneği yüklemesi için yapılandırılır. 
- DR sitesinde: veri, günlük yedeklemeleri ve paylaşılan birimler ("depolama çoğaltması" olarak işaretlenir), üretim sitesinden anlık görüntü aracılığıyla çoğaltılır. Bu birimler yalnızca yük devretme sırasında bağlanır. Daha fazla bilgi için bkz. [olağanüstü durum kurtarma yük devretme yordamı](./hana-overview-high-availability-disaster-recovery.md). 
- DR sitesinde: qa örnek yüklemesi için veri, günlük yedeklemeleri, günlük ve paylaşılan birimler ("QA örnek yüklemesi" olarak işaretlenir) yapılandırılır.
- *SKU türü ı sınıfı* için önyükleme birimi Dr düğümüne çoğaltılır.

## <a name="hsr-with-stonith-for-high-availability"></a>Yüksek kullanılabilirlik için STONITH ile HSR
 
Bu topoloji, HANA sistem çoğaltma yapılandırması için iki düğümü destekler. Bu yapılandırma yalnızca bir düğümdeki tek HANA örnekleri için desteklenir. Bu, MCOS *senaryolarının desteklenmediği anlamına* gelir.

> [!NOTE]
> 2019 Aralık itibariyle bu mimari yalnızca SUSE işletim sistemi için desteklenir.


### <a name="architecture-diagram"></a>Mimari diyagramı  

![Yüksek kullanılabilirlik için STONITH ile HSR](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | STONITH için kullanıldı |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | STONITH için kullanıldı |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|**Birincil düğümde**|
|/hana/shared/SID | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |
|**İkincil düğümde**|
|/hana/shared/SID | İkincil SID için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | İkincil SID için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | İkincil SID için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | İkincil SID için günlükleri Yinele |

### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- MCOS için: birim boyutu dağıtımı, bellekteki veritabanı boyutunu temel alır. Birden çok SID ortamında bellekteki veritabanı boyutlarının desteklendiğini öğrenmek için bkz. [genel bakış ve mimari](./hana-overview-architecture.md).
- STONITH: bir SBD, STONITH kurulumu için yapılandırılmıştır. Ancak, STONITH kullanımı isteğe bağlıdır.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Depolama çoğaltması ile HSR ve DR ile yüksek kullanılabilirlik
 
Bu topoloji, HANA sistem çoğaltma yapılandırması için iki düğümü destekler. Hem normal hem de çok yönlü DRs desteklenir. Bu konfigürasyonlar yalnızca bir düğümdeki tek HANA örnekleri için desteklenir. Bu, MCOS senaryolarının bu yapılandırmalarda *desteklenmediği* anlamına gelir.

Diyagramda, üretim işlemleri birincil siteden çalışırken QA örneği için HLI biriminin kullanıldığı DR sitesinde çok amaçlı bir senaryo gösterilmiştir. DR üzerinde yük devretme (veya yük devretme testi) sırasında, DR sitesindeki QA örneği alınır. 

### <a name="architecture-diagram"></a>Mimari diyagramı  

![Depolama çoğaltması ile HSR ve DR ile yüksek kullanılabilirlik](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | STONITH için kullanıldı |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | STONITH için kullanıldı |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|**Birincil sitedeki birincil düğümde**|
|/hana/shared/SID | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |
|**Birincil sitedeki ikincil düğümde**|
|/hana/shared/SID | İkincil SID için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | İkincil SID için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | İkincil SID için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | İkincil SID için günlükleri Yinele |
|**DR sitesinde**|
|/hana/shared/SID | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/shared/QA-SID | QA SID için HANA yüklemesi | 
|/Hana/Data/qa-SID/mnt00001 | QA SID için veri dosyaları yüklemesi | 
|/Hana/log/qa-SID/mnt00001 | QA SID için günlük dosyaları yüklemesi |
|/hana/logbackups/QA-SID | QA SID için günlükleri Yinele |

### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- MCOS için: birim boyutu dağıtımı, bellekteki veritabanı boyutunu temel alır. Birden çok SID ortamında bellekteki veritabanı boyutlarının desteklendiğini öğrenmek için bkz. [genel bakış ve mimari](./hana-overview-architecture.md).
- STONITH: bir SBD, STONITH kurulumu için yapılandırılmıştır. Ancak, STONITH kullanımı isteğe bağlıdır.
- DR sitesinde: birincil ve ikincil düğüm çoğaltması için *iki depolama birimi kümesi gereklidir* .
- DR sitesinde: birimler ve bağlama noktaları ("HANA yüklemesi için gereklidir" olarak işaretlenir), DR HLI biriminde üretim HANA örneği yüklemesi için yapılandırılır. 
- DR sitesinde: veri, günlük yedeklemeleri ve paylaşılan birimler ("depolama çoğaltması" olarak işaretlenir), üretim sitesinden anlık görüntü aracılığıyla çoğaltılır. Bu birimler yalnızca yük devretme sırasında bağlanır. Daha fazla bilgi için bkz. [olağanüstü durum kurtarma yük devretme yordamı](./hana-overview-high-availability-disaster-recovery.md). 
- DR sitesinde: qa örnek yüklemesi için veri, günlük yedeklemeleri, günlük ve paylaşılan birimler ("QA örnek yüklemesi" olarak işaretlenir) yapılandırılır.
- *SKU türü ı sınıfı* için önyükleme birimi Dr düğümüne çoğaltılır.


## <a name="host-auto-failover-11"></a>Konak otomatik yük devretme (1 + 1)
 
Bu topoloji, bir konak otomatik yük devretme yapılandırmasındaki iki düğümü destekler. Ana/çalışan rolüne ve bir bekleme moduna sahip bir düğüm vardır. *SAP bu senaryoyu yalnızca S/4 HANA için destekler.* Daha fazla bilgi için bkz. [OSS note 2408419-SAP S/4HANA-çoklu düğüm desteği](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Mimari diyagramı  

![Konak otomatik yük devretme (1 + 1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Düğümden düğüme iletişim |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Düğümden düğüme iletişim |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|**Ana ve bekleme düğümlerinde**|
|/Hana/Shared | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |



### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- Bekleme durumunda: birim ve bağlama noktaları, bir HANA örneği yüklemesi için bekleme biriminde yapılandırılır ("HANA yüklemesi için gerekli" olarak işaretlenir).
 

## <a name="scale-out-with-standby"></a>Bekleme ile genişleme
 
Bu topoloji, genişleme yapılandırmasındaki birden çok düğümü destekler. Ana role sahip bir düğüm, çalışan rolüne sahip bir veya daha fazla düğüm ve bekleme olarak bir veya daha fazla düğüm vardır. Ancak, herhangi bir zamanda tek bir noktada yalnızca bir ana düğüm olabilir.


### <a name="architecture-diagram"></a>Mimari diyagramı  

![Bekleme ile genişleme](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Düğümden düğüme iletişim |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Düğümden düğüme iletişim |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|**Ana, çalışan ve bekleme düğümlerinde**|
|/Hana/Shared | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |


## <a name="scale-out-without-standby"></a>Bekleme olmadan genişleme
 
Bu topoloji, genişleme yapılandırmasındaki birden çok düğümü destekler. Ana role sahip bir düğüm ve bir çalışan rolüne sahip bir veya daha fazla düğüm vardır. Ancak, herhangi bir zamanda tek bir noktada yalnızca bir ana düğüm olabilir.


### <a name="architecture-diagram"></a>Mimari diyagramı  

![Bekleme olmadan genişleme](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Düğümden düğüme iletişim |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Düğümden düğüme iletişim |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|**Ana ve çalışan düğümlerinde**|
|/Hana/Shared | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |


### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.

## <a name="scale-out-with-dr-using-storage-replication"></a>Depolama çoğaltması kullanarak DR ile genişleme
 
Bu topoloji, bir DR ile bir ölçekte birden çok düğümü destekler. Hem normal hem de çok yönlü DRs desteklenir. Diyagramda yalnızca tek amaçlı DR gösterilmiştir. Bu topolojiyi, bekleme düğümü olmadan veya olmadan isteyebilirsiniz.


### <a name="architecture-diagram"></a>Mimari diyagramı  

![Depolama çoğaltması kullanarak DR ile genişleme](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Düğümden düğüme iletişim |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Düğümden düğüme iletişim |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|**Birincil düğümde**|
|/Hana/Shared | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |
|**DR düğümünde**|
|/Hana/Shared | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 


### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
-  DR sitesinde: birimler ve bağlama noktaları ("HANA yüklemesi için gereklidir" olarak işaretlenir), DR HLI biriminde üretim HANA örneği yüklemesi için yapılandırılır. 
- DR sitesinde: veri, günlük yedeklemeleri ve paylaşılan birimler ("depolama çoğaltması" olarak işaretlenir), üretim sitesinden anlık görüntü aracılığıyla çoğaltılır. Bu birimler yalnızca yük devretme sırasında bağlanır. Daha fazla bilgi için bkz. [olağanüstü durum kurtarma yük devretme yordamı](./hana-overview-high-availability-disaster-recovery.md). 
- *SKU türü ı sınıfı* için önyükleme birimi Dr düğümüne çoğaltılır.


## <a name="single-node-with-dr-using-hsr"></a>HSR kullanarak DR ile tek düğüm
 
Bu topoloji, bir SID ile bir genişleme yapılandırmasındaki bir düğümü destekler ve bir birincil SID için DR sitesine HANA sistem çoğaltmasını sağlar. Diyagramda, birincil sitede yalnızca bir tek SID sistemi gösterilmiştir, ancak çoklu SID (MCOS) sistemleri de desteklenir.

### <a name="architecture-diagram"></a>Mimari diyagramı  

![HSR kullanarak DR ile tek düğüm](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI/HSR |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI/HSR |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları, HLI birimlerinde (birincil ve DR) önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|/hana/shared/SID | SID için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | SID için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | SID için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | SID için günlükleri Yinele |


### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- MCOS için: birim boyutu dağıtımı, bellekteki veritabanı boyutunu temel alır. Birden çok SID ortamında bellekteki veritabanı boyutlarının desteklendiğini öğrenmek için bkz. [genel bakış ve mimari](./hana-overview-architecture.md).
- Birincil düğüm, HANA sistem çoğaltmasını kullanarak DR düğümüyle eşitlenir. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) , bölge ağlarınızla özel bir ağ oluşturmak üzere ExpressRoute devreleri birbirine bağlamak için kullanılır.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>Tek düğümlü HSR-DR (maliyet için iyileştirilmiş) 
 
 Bu topoloji, bir SID ile bir genişleme yapılandırmasındaki bir düğümü destekler ve bir birincil SID için DR sitesine HANA sistem çoğaltmasını sağlar. Diyagramda, birincil sitede yalnızca bir tek SID sistemi gösterilmiştir, ancak çoklu SID (MCOS) sistemleri de desteklenir. DR sitesinde, üretim işlemleri birincil siteden çalışırken QA örneği için bir HLı birimi kullanılır. DR üzerinde yük devretme (veya yük devretme testi) sırasında, DR sitesindeki QA örneği alınır.

### <a name="architecture-diagram"></a>Mimari diyagramı  

![Tek düğümlü HSR-DR (maliyet için iyileştirilmiş)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI/HSR |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI/HSR |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|**Birincil sitede**|
|/hana/shared/SID | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |
|**DR sitesinde**|
|/hana/shared/SID | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |
|/hana/shared/QA-SID | QA SID için HANA yüklemesi | 
|/Hana/Data/qa-SID/mnt00001 | QA SID için veri dosyaları yüklemesi | 
|/Hana/log/qa-SID/mnt00001 | QA SID için günlük dosyaları yüklemesi |
|/hana/logbackups/QA-SID | QA SID için günlükleri Yinele |

### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- MCOS için: birim boyutu dağıtımı, bellekteki veritabanı boyutunu temel alır. Birden çok SID ortamında bellekteki veritabanı boyutlarının desteklendiğini öğrenmek için bkz. [genel bakış ve mimari](./hana-overview-architecture.md).
- DR sitesinde: birimler ve bağlama noktaları, DR HLI biriminde üretim HANA örneği yüklemesi için yapılandırılır (DR sitesinde "ÜRETIM örneği" olarak işaretlenir). 
- DR sitesinde: qa örnek yüklemesi için veri, günlük yedeklemeleri, günlük ve paylaşılan birimler ("QA örnek yüklemesi" olarak işaretlenir) yapılandırılır.
- Birincil düğüm, HANA sistem çoğaltmasını kullanarak DR düğümüyle eşitlenir. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) , bölge ağlarınızla özel bir ağ oluşturmak üzere ExpressRoute devreleri birbirine bağlamak için kullanılır.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>HSR ile yüksek kullanılabilirlik ve olağanüstü durum kurtarma 
 
 Bu topoloji, yerel bölgelerin yüksek kullanılabilirliği için HANA sistem çoğaltma yapılandırması için iki düğümü destekler. DR bölgesindeki üçüncü düğüm, HSR (zaman uyumsuz mod) kullanarak birincil siteyle eşitlenir. 

### <a name="architecture-diagram"></a>Mimari diyagramı  

![HSR ile yüksek kullanılabilirlik ve olağanüstü durum kurtarma](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI/HSR |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI/HSR |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|**Birincil sitede**|
|/hana/shared/SID | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |
|**DR sitesinde**|
|/hana/shared/SID | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |


### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- DR sitesinde: birimler ve bağlama noktaları, DR HLI biriminde üretim HANA örneği yüklemesi için yapılandırılır ("ÜRETIM DR örneği" olarak işaretlenir). 
- Birincil site düğümü, HANA sistem çoğaltmasını kullanarak DR düğümüyle eşitlenir. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) , bölge ağlarınızla özel bir ağ oluşturmak üzere ExpressRoute devreleri birbirine bağlamak için kullanılır.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>HSR ile yüksek kullanılabilirlik ve olağanüstü durum kurtarma (maliyet için iyileştirilmiş)
 
 Bu topoloji, yerel bölgelerin yüksek kullanılabilirliği için HANA sistem çoğaltma yapılandırması için iki düğümü destekler. Dr bölgesindeki üçüncü düğüm, diğer bir örnek (örneğin, QA) DR düğümünden zaten çalıştırıldığı sırada DR bölgesindeki üçüncü düğüm, HSR (Async modu) kullanarak birincil siteyle eşitlenir. 

### <a name="architecture-diagram"></a>Mimari diyagramı  

![HSR ile yüksek kullanılabilirlik ve olağanüstü durum kurtarma (maliyet için iyileştirilmiş)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI/HSR |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI/HSR |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|**Birincil sitede**|
|/hana/shared/SID | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |
|**DR sitesinde**|
|/hana/shared/SID | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |
|/hana/shared/QA-SID | QA SID için HANA yüklemesi | 
|/Hana/Data/qa-SID/mnt00001 | QA SID için veri dosyaları yüklemesi | 
|/Hana/log/qa-SID/mnt00001 | QA SID için günlük dosyaları yüklemesi |
|/hana/logbackups/QA-SID | QA SID için günlükleri Yinele |

### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- DR sitesinde: birimler ve bağlama noktaları, DR HLI biriminde üretim HANA örneği yüklemesi için yapılandırılır ("ÜRETIM DR örneği" olarak işaretlenir). 
- DR sitesinde: qa örnek yüklemesi için veri, günlük yedeklemeleri, günlük ve paylaşılan birimler ("QA örnek yüklemesi" olarak işaretlenir) yapılandırılır.
- Birincil site düğümü, HANA sistem çoğaltmasını kullanarak DR düğümüyle eşitlenir. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) , bölge ağlarınızla özel bir ağ oluşturmak üzere ExpressRoute devreleri birbirine bağlamak için kullanılır.

## <a name="scale-out-with-dr-using-hsr"></a>HSR kullanarak DR ile ölçeklendirme
 
Bu topoloji, bir DR ile bir ölçekte birden çok düğümü destekler. Bu topolojiyi, bekleme düğümü olmadan veya olmadan isteyebilirsiniz. Birincil site düğümü, HANA sistem çoğaltması (zaman uyumsuz mod) kullanılarak DR sitesi düğümüyle eşitlenir.


### <a name="architecture-diagram"></a>Mimari diyagramı  

[![HSR kullanarak Dr ile ölçeklendirme](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC mantıksal arabirimi | SKU türü | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR ı | eth0. Tenant | eno1. Tenant | İstemciden HLI/HSR |
| B | TÜR ı | eth2. Tenant | eno3. Tenant | Düğümden düğüme iletişim |
| C | TÜR ı | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR ı | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR ıı | VLAN\<tenantNo> | team0. Tenant | İstemciden HLI/HSR |
| B | TÜR ıı | VLAN\<tenantNo+2> | team0. Tenant + 2 | Düğümden düğüme iletişim |
| C | TÜR ıı | VLAN\<tenantNo+1> | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR ıı | VLAN\<tenantNo+3> | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Bağlama noktası | Kullanım örneği | 
| --- | --- |
|**Birincil düğümde**|
|/Hana/Shared | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |
|**DR düğümünde**|
|/Hana/Shared | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |


### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- DR sitesinde: birimler ve bağlama noktaları, DR HLI biriminde üretim HANA örneği yüklemesi için yapılandırılır. 
- Birincil site düğümü, HANA sistem çoğaltmasını kullanarak DR düğümüyle eşitlenir. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) , bölge ağlarınızla özel bir ağ oluşturmak üzere ExpressRoute devreleri birbirine bağlamak için kullanılır.


## <a name="next-steps"></a>Sonraki adımlar
- HANA büyük örnekleri için [altyapı ve bağlantı](./hana-overview-infrastructure-connectivity.md)
- HANA büyük örnekleri için [yüksek kullanılabilirlik ve olağanüstü durum kurtarma](./hana-overview-high-availability-disaster-recovery.md)
