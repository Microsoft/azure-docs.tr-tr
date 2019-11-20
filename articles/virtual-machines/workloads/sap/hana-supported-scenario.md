---
title: Desteklenen senaryolar SAP HANA Azure 'da (büyük örnekler) | Microsoft Docs
description: Desteklenen senaryolar ve Azure 'daki SAP HANA için mimari ayrıntıları (büyük örnekler)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/06/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f17e447f26ae4f7573941fc0c578a918ff45a145
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101226"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA büyük örnekleri için desteklenen senaryolar
Bu belgede, desteklenen senaryolar, HANA büyük örnekleri (HLI) için mimari ayrıntılarının birlikte açıklanmaktadır.

>[!NOTE]
>Gerekli senaryonuz burada bahsedilmez, gereksinimlerinizi değerlendirmek için Microsoft hizmet yönetimi ekibine başvurun.
HLI birim sağlamaya devam etmeden önce, tasarımı SAP veya hizmet uygulama iş ortağınız ile doğrulayın.

## <a name="terms-and-definitions"></a>Hüküm ve tanımlar
Belgede kullanılan hüküm ve tanımları anlayalim.

- SID HANA sistemi için sistem tanımlayıcısı.
- HLI: Hana büyük örnekleri.
- GERÇEKLEŞEN Bir olağanüstü durum kurtarma sitesi.
- Normal DR: Yalnızca DR amacı kullanılan özel bir kaynağa sahip bir sistem kurulumu.
- Çok amaçlı DR: Dr sitesindeki bir sistem, üretim dışı ortam kullanmak üzere yapılandırılmış üretim örneği ile birlikte, DR olayına kullanılmak üzere yapılandırılmıştır. 
- Tek SID:  Bir örneği yüklenmiş bir sistem.
- Çoklu SID: Birden çok örneği yapılandırılmış bir sistem. MCOS ortamı da denir.


## <a name="overview"></a>Genel Bakış
HANA büyük örnekler, iş gereksinimlerinizi gerçekleştirmek için çeşitli mimarilerin kullanımını destekler. Aşağıdaki liste, senaryoları ve bunların yapılandırma ayrıntılarını içerir. 

Türetilmiş mimari tasarımı yalnızca altyapı perspektifinden, SAP veya HANA dağıtımı için uygulama iş ortaklarınıza danışmanız gerekir. Senaryolarınız listede yoksa, mimariyi gözden geçirmek ve sizin için bir çözüm türetmek Microsoft hesabı ekibine başvurun.

**Bu mimariler, bir TDı (özel veri tümleştirme) tasarımıyla ve SAP tarafından desteklenmeyle tamamen uyumludur.**

Bu belgede, desteklenen her mimarideki iki bileşenin ayrıntıları açıklanmaktadır:

- Ethernet
- Depolama

### <a name="ethernet"></a>Ethernet

Sağlanan her sunucu, Ethernet arabirimi kümeleriyle önceden yapılandırılmış olarak sunulur. Her bir HLI birimde yapılandırılan Ethernet arabirimlerinin ayrıntıları aşağıda verilmiştir.

- Y: Bu arabirim, istemci erişimi için/tarafından kullanılır.
- **B**: Bu arabirim düğümden düğüme iletişim için kullanılır. Bu arabirim, tüm sunucularda yapılandırılır (istenen topolojiden bağımsız olarak), ancak yalnızca 
- genişleme senaryoları.
- **C**: Bu arabirim, düğüm için depolama bağlantısı için kullanılır.
- **D**: Bu arabirim, STONITH kurulumu için düğüm için ISCSı cihaz bağlantısı için kullanılır. Bu arabirim yalnızca HSR kurulumu istendiğinde yapılandırılır.  

| NIC MANTIKSAL ARABIRIMLERI | SKU TÜRÜ | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR I | eth0. Tenant | eno1. Tenant | İstemciden HLı 'e |
| B | TÜR I | eth2. Tenant | eno3. Tenant | Düğümden düğüme |
| C | TÜR I | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR I | eth4. Tenant | eno4. Tenant | STONITH |
| A | TÜR II | VLAN\<tenantno > | team0. Tenant | İstemciden HLı 'e |
| B | TÜR II | VLAN\<tenantno + 2 > | team0. Tenant + 2 | Düğümden düğüme |
| C | TÜR II | VLAN\<tenantno + 1 > | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR II | VLAN\<tenantno + 3 > | team0. Tenant + 3 | STONITH |

Arabirimleri, HLI biriminde yapılandırılan topolojiye göre kullanırsınız. Örneğin, "B" arabirimi düğümden düğüme iletişim için ayarlanır ve bu, yapılandırılmış bir genişleme topolojisi olduğunda faydalıdır. Tek düğümlü ölçek artırma yapılandırması durumunda bu arabirim kullanılmaz. Arabirim kullanımı hakkında daha fazla bilgi edinmek için gerekli senaryolarınızı (Bu belgenin ilerleyen kısımlarında) gözden geçirin. 

Gerekirse, sizin için Ek NIC kartları tanımlayabilirsiniz. Ancak, mevcut NIC 'lerde yapılandırma değiştirilemez.

>[!NOTE]
>Hala fiziksel arabirimler veya bonus olan ek arabirimler bulabilirsiniz. Kullandığınız durum için yukarıda bahsedilen arabirimleri göz önünde bulundurmanız gerekir, Rest yok sayılır/veya ile birlikte kullanılamaz.

İki IP adresi atanmış birimlerin dağıtımı şöyle görünmelidir:

- Ethernet "A", Microsoft 'a gönderdiğiniz sunucu IP havuzu adres aralığının dışında atanmış bir IP adresine sahip olmalıdır. Bu IP adresi, işletim sisteminin/etc/konaklarında bakım için kullanılacaktır.

- Ethernet "C", NFS iletişimi için kullanılan bir IP adresi atanmış olmalıdır. Bu nedenle, örneğin kiracının kiracı içindeki trafiği örneğine izin vermek için bu adreslerin vs/Konakları içinde tutulması gerekmez.

HANA sistem çoğaltması veya HANA genişleme dağıtım durumlarında, iki IP adresi atanmış bir dikey pencere yapılandırması uygun değildir. Yalnızca iki IP adresi atanırsa ve böyle bir yapılandırmayı dağıtmak istiyorsanız, atanan üçüncü bir VLAN 'da üçüncü bir IP adresi almak için Azure hizmet yönetimi 'ndeki SAP HANA başvurun. Üç NIC bağlantı noktasında üç IP adresi atanmış olan HANA büyük örnek birimlerinde aşağıdaki kullanım kuralları geçerlidir:

- Ethernet "A", Microsoft 'a gönderdiğiniz sunucu IP havuzu adres aralığının dışında atanmış bir IP adresine sahip olmalıdır. Bu nedenle bu IP adresi, işletim sisteminin/etc/konaklarında bakım için kullanılmamalıdır.

- Ethernet "B", farklı örnekler arasındaki iletişim için özel olarak ve ana bilgisayarlarda tutulmak üzere kullanılmalıdır. Bu adresler, HANA 'nın düğümler arası yapılandırma için kullandığı IP adresleri olarak genişleme HANA yapılandırmalarında tutulması gereken IP adresleri de olacaktır.

- Ethernet "C", NFS depolama iletişimi için kullanılan bir IP adresine atanmalıdır. Bu nedenle, bu tür adresler vs/konaklarında tutulmamalıdır.

- "D", pacemaker için erişim STONITH cihazında özel olarak kullanılmalıdır. Bu arabirim, HANA sistem çoğaltmasını (HSR) yapılandırırken ve bir SBD tabanlı cihaz kullanarak işletim sisteminde otomatik yük devretme elde etmek istediğinizde gereklidir.


### <a name="storage"></a>Depolama
Depolama, istenen topolojiye göre önceden yapılandırılmıştır. Birim boyutları ve bağlama noktası, yapılandırılan sunucu, SKU 'Lar ve topoloji sayısına göre değişir. Daha fazla bilgi edinmek için gerekli senaryolarınızı gözden geçirin (Bu belgede daha sonra). Daha fazla depolama gerekliyse, bu alanı bir TB artışında satın alabilirsiniz.

>[!NOTE]
>Bağlamanoktası/usr/SAP/\<SID >,/Hana/Shared Mount noktasına yönelik sembolik bir bağlantıdır.


## <a name="supported-scenarios"></a>Desteklenen senaryolar

Mimari diyagramlarda, grafikler için aşağıdaki gösterimler kullanılır:

![Göstergeler. PNG](media/hana-supported-scenario/Legends.PNG)

Aşağıdaki listede desteklenen senaryolar gösterilmektedir:

1. Tek bir SID içeren tek düğüm
2. Tek düğümlü MCOS
3. DR ile tek düğüm (normal)
4. DR (çok amaçlı) ile tek düğüm
5. STONITH ile HSR
6. DR ile HSR (normal/çok amaçlı) 
7. Konak otomatik yük devretme (1 + 1) 
8. Bekleme ile genişleme
9. Bekleme olmadan genişleme
10. DR ile ölçeklendirme



## <a name="1-single-node-with-one-sid"></a>1. Tek bir SID içeren tek düğüm

Bu topoloji, bir SID ile ölçek artırma yapılandırmasındaki bir düğümü destekler.

### <a name="architecture-diagram"></a>Mimari diyagramı  

![Single-node-with-one-SID. png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC MANTIKSAL ARABIRIMLERI | SKU TÜRÜ | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR I | eth0. Tenant | eno1. Tenant | İstemciden HLı 'e |
| B | TÜR I | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR I | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR I | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR II | VLAN\<tenantno > | team0. Tenant | İstemciden HLı 'e |
| B | TÜR II | VLAN\<tenantno + 2 > | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR II | VLAN\<tenantno + 1 > | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR II | VLAN\<tenantno + 3 > | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Başlatma noktası | Kullanım örneği | 
| --- | --- |
|/hana/shared/SID | HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Günlükleri Yinele |

### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.

## <a name="2-single-node-mcos"></a>2. Tek düğümlü MCOS

Bu topoloji, birden fazla SID ile ölçek artırma yapılandırmasındaki bir düğümü destekler.

### <a name="architecture-diagram"></a>Mimari diyagramı  

![Single-node-mcos. png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC MANTIKSAL ARABIRIMLERI | SKU TÜRÜ | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR I | eth0. Tenant | eno1. Tenant | İstemciden HLı 'e |
| B | TÜR I | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR I | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR I | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR II | VLAN\<tenantno > | team0. Tenant | İstemciden HLı 'e |
| B | TÜR II | VLAN\<tenantno + 2 > | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR II | VLAN\<tenantno + 1 > | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR II | VLAN\<tenantno + 3 > | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Başlatma noktası | Kullanım örneği | 
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
- Birim boyutu dağıtımı, bellekteki veritabanı boyutunu temel alarak. Çoklu SID ortamında bellekteki veritabanı boyutlarının desteklendiğini öğrenmek için [genel bakış ve mimari](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) bölümüne bakın.

## <a name="3-single-node-with-dr-normal"></a>3. DR ile tek düğüm (normal)
 
Bu topoloji, bir veya daha fazla SID ile bir veya birden çok SID içeren bir düğümü, birincil SID için DR sitesine depolama tabanlı çoğaltma ile destekler. Diyagramda, birincil sitede yalnızca tek SID gösterilmiştir, ancak multisıd (MCOS) de desteklenir.

### <a name="architecture-diagram"></a>Mimari diyagramı  

![Single-node-with-Dr. png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC MANTIKSAL ARABIRIMLERI | SKU TÜRÜ | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR I | eth0. Tenant | eno1. Tenant | İstemciden HLı 'e |
| B | TÜR I | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR I | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR I | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR II | VLAN\<tenantno > | team0. Tenant | İstemciden HLı 'e |
| B | TÜR II | VLAN\<tenantno + 2 > | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR II | VLAN\<tenantno + 1 > | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR II | VLAN\<tenantno + 3 > | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Başlatma noktası | Kullanım örneği | 
| --- | --- |
|/hana/shared/SID | SID için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | SID için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | SID için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | SID için günlükleri Yinele |


### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- MCOS için: Birim boyutu dağıtımı, bellekteki veritabanı boyutunu temel alarak. Çoklu SID ortamında bellekteki veritabanı boyutlarının desteklendiğini öğrenmek için [genel bakış ve mimari](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) bölümüne bakın.
- DR: Birimler ve bağlama noktaları, DR HLI biriminde üretim HANA örneği yüklemesi için yapılandırılır ("HANA yüklemesi için gerekli" olarak işaretlenir). 
- DR: Veriler, logbackups ve paylaşılan birimler ("depolama çoğaltması" olarak işaretlenen), üretim sitesinden anlık görüntü aracılığıyla çoğaltılır. Bu birimler yalnızca yük devretme zamanı sırasında bağlanır. Daha fazla bilgi için, daha fazla ayrıntı için belge [olağanüstü durum kurtarma yük devretme yordamını](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) okuyun.
- **SKU türü ı sınıfı** için önyükleme birimi Dr düğümüne çoğaltılır.


## <a name="4-single-node-with-dr-multipurpose"></a>4. DR (çok amaçlı) ile tek düğüm
 
Bu topoloji, bir veya daha fazla SID ile bir veya birden çok SID içeren bir düğümü, birincil SID için DR sitesine depolama tabanlı çoğaltma ile destekler. Diyagramda, birincil sitede yalnızca tek SID gösterilmiştir, ancak multisıd (MCOS) de desteklenir. DR sitesinde, birincil siteden üretim işlemleri çalışırken QA örneği için HLı birimi kullanılır. DR üzerinde yük devretme (veya yük devretme testi) sırasında, DR sitesindeki QA örneği alınır.

### <a name="architecture-diagram"></a>Mimari diyagramı  

![Single-node-with-Dr-Multipurpose. png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC MANTIKSAL ARABIRIMLERI | SKU TÜRÜ | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR I | eth0. Tenant | eno1. Tenant | İstemciden HLı 'e |
| B | TÜR I | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR I | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR I | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR II | VLAN\<tenantno > | team0. Tenant | İstemciden HLı 'e |
| B | TÜR II | VLAN\<tenantno + 2 > | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR II | VLAN\<tenantno + 1 > | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR II | VLAN\<tenantno + 3 > | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Başlatma noktası | Kullanım örneği | 
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
|/hana/shared/QA-SID | QA için HANA yüklemesi SID | 
|/Hana/Data/qa-SID/mnt00001 | QA SID için veri dosyaları yüklemesi | 
|/Hana/log/qa-SID/mnt00001 | QA SID için günlük dosyaları yüklemesi |
|/hana/logbackups/QA-SID | QA SID için günlükleri Yinele |

### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- MCOS için: Birim boyutu dağıtımı, bellekteki veritabanı boyutunu temel alarak. Çoklu SID ortamında bellekteki veritabanı boyutlarının desteklendiğini öğrenmek için [genel bakış ve mimari](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) bölümüne bakın.
- DR: Birimler ve bağlama noktaları, DR HLI biriminde üretim HANA örneği yüklemesi için yapılandırılır ("HANA yüklemesi için gerekli" olarak işaretlenir). 
- DR: Veriler, logbackups ve paylaşılan birimler ("depolama çoğaltması" olarak işaretlenen), üretim sitesinden anlık görüntü aracılığıyla çoğaltılır. Bu birimler yalnızca yük devretme zamanı sırasında bağlanır. Daha fazla bilgi için, daha fazla ayrıntı için belge [olağanüstü durum kurtarma yük devretme yordamını](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) okuyun. 
- DR: Qa örnek yüklemesi için veri, logbackups, log, paylaşılan birimler ("QA örnek yüklemesi" olarak işaretlenir) yapılandırılır.
- **SKU türü ı sınıfı** için önyükleme birimi Dr düğümüne çoğaltılır.

## <a name="5-hsr-with-stonith"></a>5. STONITH ile HSR
 
Bu topoloji, HANA sistem çoğaltması (HSR) yapılandırması için iki düğümü destekler. Bu yapılandırma yalnızca bir düğümdeki tek HANA örnekleri için desteklenir. Anlamına gelir, MCOS senaryoları desteklenmez.

**Şimdilik bu mimari yalnızca SUSE Işletim sistemi için desteklenir.**


### <a name="architecture-diagram"></a>Mimari diyagramı  

![HSR-with-STONITH. png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC MANTIKSAL ARABIRIMLERI | SKU TÜRÜ | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR I | eth0. Tenant | eno1. Tenant | İstemciden HLı 'e |
| B | TÜR I | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR I | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR I | eth4. Tenant | eno4. Tenant | STONITH için kullanıldı |
| A | TÜR II | VLAN\<tenantno > | team0. Tenant | İstemciden HLı 'e |
| B | TÜR II | VLAN\<tenantno + 2 > | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR II | VLAN\<tenantno + 1 > | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR II | VLAN\<tenantno + 3 > | team0. Tenant + 3 | STONITH için kullanıldı |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Başlatma noktası | Kullanım örneği | 
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
- MCOS için: Birim boyutu dağıtımı, bellekteki veritabanı boyutunu temel alarak. Çoklu SID ortamında bellekteki veritabanı boyutlarının desteklendiğini öğrenmek için [genel bakış ve mimari](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) bölümüne bakın.
- STONITH Bir SBD, STONITH kurulumu için yapılandırılmıştır. Ancak, STONITH kullanımı isteğe bağlıdır.


## <a name="6-hsr-with-dr"></a>6. DR ile HSR
 
Bu topoloji, HANA sistem çoğaltması (HSR) yapılandırması için iki düğümü destekler. Hem normal hem de çok amaçlı DR desteklenir. Bu konfigürasyonlar yalnızca bir düğümdeki tek HANA örnekleri için desteklenir. Yani, MCOS senaryoları bu yapılandırmalarda desteklenmez.

Diyagramda, üretim işlemleri birincil siteden çalışırken, DR sitesinde, HLı birimi de QA örneği için kullanılan çok amaçlı senaryo gösterilmiştir. DR üzerinde yük devretme (veya yük devretme testi) sırasında, DR sitesindeki QA örneği alınır. 



### <a name="architecture-diagram"></a>Mimari diyagramı  

![HSR-with-DR. png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC MANTIKSAL ARABIRIMLERI | SKU TÜRÜ | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR I | eth0. Tenant | eno1. Tenant | İstemciden HLı 'e |
| B | TÜR I | eth2. Tenant | eno3. Tenant | Yapılandırıldı ancak kullanımda değil |
| C | TÜR I | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR I | eth4. Tenant | eno4. Tenant | STONITH için kullanıldı |
| A | TÜR II | VLAN\<tenantno > | team0. Tenant | İstemciden HLı 'e |
| B | TÜR II | VLAN\<tenantno + 2 > | team0. Tenant + 2 | Yapılandırıldı ancak kullanımda değil |
| C | TÜR II | VLAN\<tenantno + 1 > | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR II | VLAN\<tenantno + 3 > | team0. Tenant + 3 | STONITH için kullanıldı |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Başlatma noktası | Kullanım örneği | 
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
|/hana/shared/QA-SID | QA için HANA yüklemesi SID | 
|/Hana/Data/qa-SID/mnt00001 | QA SID için veri dosyaları yüklemesi | 
|/Hana/log/qa-SID/mnt00001 | QA SID için günlük dosyaları yüklemesi |
|/hana/logbackups/QA-SID | QA SID için günlükleri Yinele |

### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- MCOS için: Birim boyutu dağıtımı, bellekteki veritabanı boyutunu temel alarak. Çoklu SID ortamında bellekteki veritabanı boyutlarının desteklendiğini öğrenmek için [genel bakış ve mimari](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) bölümüne bakın.
- STONITH Bir SBD, STONITH kurulumu için yapılandırılmıştır. Ancak, STONITH kullanımı isteğe bağlıdır.
- DR: Birincil ve ikincil düğüm çoğaltması için **iki depolama birimi kümesi gereklidir** .
- DR: Birimler ve bağlama noktaları, DR HLI biriminde üretim HANA örneği yüklemesi için yapılandırılır ("HANA yüklemesi için gerekli" olarak işaretlenir). 
- DR: Veriler, logbackups ve paylaşılan birimler ("depolama çoğaltması" olarak işaretlenen), üretim sitesinden anlık görüntü aracılığıyla çoğaltılır. Bu birimler yalnızca yük devretme zamanı sırasında bağlanır. Daha fazla bilgi için, daha fazla ayrıntı için belge [olağanüstü durum kurtarma yük devretme yordamını](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) okuyun. 
- DR: Qa örnek yüklemesi için veri, logbackups, log, paylaşılan birimler ("QA örnek yüklemesi" olarak işaretlenir) yapılandırılır.
- **SKU türü ı sınıfı** için önyükleme birimi Dr düğümüne çoğaltılır.


## <a name="7-host-auto-failover-11"></a>7. Konak otomatik yük devretme (1 + 1)
 
Bu topoloji, bir konak otomatik yük devretme yapılandırmasındaki iki düğümü destekler. Ana/çalışan rolüne ve bekleme moduna sahip bir düğüm vardır. **SAP bu senaryoyu yalnızca S/4 HANA için destekler.** Daha fazla ayrıntı için bkz. OSS Note "[2408419-SAP S/4HANA-çoklu düğüm desteği](https://launchpad.support.sap.com/#/notes/2408419)".



### <a name="architecture-diagram"></a>Mimari diyagramı  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC MANTIKSAL ARABIRIMLERI | SKU TÜRÜ | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR I | eth0. Tenant | eno1. Tenant | İstemciden HLı 'e |
| B | TÜR I | eth2. Tenant | eno3. Tenant | Düğümden düğüme iletişim |
| C | TÜR I | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR I | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR II | VLAN\<tenantno > | team0. Tenant | İstemciden HLı 'e |
| B | TÜR II | VLAN\<tenantno + 2 > | team0. Tenant + 2 | Düğümden düğüme iletişim |
| C | TÜR II | VLAN\<tenantno + 1 > | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR II | VLAN\<tenantno + 3 > | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Başlatma noktası | Kullanım örneği | 
| --- | --- |
|**Ana ve bekleme düğümlerinde**|
|/Hana/Shared | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |



### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.
- Bekleme durumunda: Birimler ve bağlama noktaları, bekleme biriminde HANA örneği yüklemesi için yapılandırılır ("HANA yüklemesi için gerekli" olarak işaretlenir).
 

## <a name="8-scale-out-with-standby"></a>8. Bekleme ile genişleme
 
Bu topoloji, genişleme yapılandırmasındaki birden çok düğümü destekler. Ana role sahip bir düğüm, çalışan rolüne sahip bir veya daha fazla düğüm ve bekleme olarak bir veya daha fazla düğüm vardır. Ancak, belirli bir zaman noktasında yalnızca bir ana düğüm olabilir.


### <a name="architecture-diagram"></a>Mimari diyagramı  

![scaleout-nm-standby. png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC MANTIKSAL ARABIRIMLERI | SKU TÜRÜ | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR I | eth0. Tenant | eno1. Tenant | İstemciden HLı 'e |
| B | TÜR I | eth2. Tenant | eno3. Tenant | Düğümden düğüme iletişim |
| C | TÜR I | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR I | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR II | VLAN\<tenantno > | team0. Tenant | İstemciden HLı 'e |
| B | TÜR II | VLAN\<tenantno + 2 > | team0. Tenant + 2 | Düğümden düğüme iletişim |
| C | TÜR II | VLAN\<tenantno + 1 > | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR II | VLAN\<tenantno + 3 > | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Başlatma noktası | Kullanım örneği | 
| --- | --- |
|**Ana, çalışan ve bekleme düğümlerinde**|
|/Hana/Shared | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |


## <a name="9-scale-out-without-standby"></a>9. Bekleme olmadan genişleme
 
Bu topoloji, genişleme yapılandırmasındaki birden çok düğümü destekler. Ana role sahip bir düğüm ve çalışan rolüne sahip bir veya mod düğümleri vardır. Ancak, belirli bir zaman noktasında yalnızca bir ana düğüm olabilir.


### <a name="architecture-diagram"></a>Mimari diyagramı  

![scaleout-Nm. png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC MANTIKSAL ARABIRIMLERI | SKU TÜRÜ | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR I | eth0. Tenant | eno1. Tenant | İstemciden HLı 'e |
| B | TÜR I | eth2. Tenant | eno3. Tenant | Düğümden düğüme iletişim |
| C | TÜR I | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR I | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR II | VLAN\<tenantno > | team0. Tenant | İstemciden HLı 'e |
| B | TÜR II | VLAN\<tenantno + 2 > | team0. Tenant + 2 | Düğümden düğüme iletişim |
| C | TÜR II | VLAN\<tenantno + 1 > | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR II | VLAN\<tenantno + 3 > | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Başlatma noktası | Kullanım örneği | 
| --- | --- |
|**Ana ve çalışan düğümlerinde**|
|/Hana/Shared | Üretim SID 'SI için HANA yüklemesi | 
|/Hana/Data/SID/mnt00001 | Üretim SID 'SI için veri dosyaları yüklemesi | 
|/Hana/log/SID/mnt00001 | Üretim SID 'SI için günlük dosyaları yüklemesi | 
|/hana/logbackups/SID | Üretim SID 'SI için günlükleri Yinele |


### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular
- /Usr/SAP/SID,/Hana/Shared/sıdöğesine yönelik sembolik bir bağlantıdır.

## <a name="10-scale-out-with-dr"></a>10. DR ile ölçeklendirme
 
Bu topoloji, bir DR ile bir ölçekte birden çok düğümü destekler. Hem normal hem de çok amaçlı DR desteklenir. Diyagramda yalnızca tek amaçlı DR gösterilmiştir. Bu topolojiyi, bekleme düğümü olmadan veya olmadan isteyebilirsiniz.


### <a name="architecture-diagram"></a>Mimari diyagramı  

![scaleout-with-Dr. png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Aşağıdaki ağ arabirimleri önceden yapılandırılmıştır:

| NIC MANTIKSAL ARABIRIMLERI | SKU TÜRÜ | SUSE OS adı | RHEL işletim sistemi ile ad | Kullanım örneği|
| --- | --- | --- | --- | --- |
| A | TÜR I | eth0. Tenant | eno1. Tenant | İstemciden HLı 'e |
| B | TÜR I | eth2. Tenant | eno3. Tenant | Düğümden düğüme iletişim |
| C | TÜR I | eth1. Tenant | eno2. Tenant | Düğümden depolamaya |
| D | TÜR I | eth4. Tenant | eno4. Tenant | Yapılandırıldı ancak kullanımda değil |
| A | TÜR II | VLAN\<tenantno > | team0. Tenant | İstemciden HLı 'e |
| B | TÜR II | VLAN\<tenantno + 2 > | team0. Tenant + 2 | Düğümden düğüme iletişim |
| C | TÜR II | VLAN\<tenantno + 1 > | team0. Tenant + 1 | Düğümden depolamaya |
| D | TÜR II | VLAN\<tenantno + 3 > | team0. Tenant + 3 | Yapılandırıldı ancak kullanımda değil |

### <a name="storage"></a>Depolama
Aşağıdaki bağlama noktaları önceden yapılandırılmıştır:

| Başlatma noktası | Kullanım örneği | 
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
-  DR: Birimler ve bağlama noktaları, DR HLI biriminde üretim HANA örneği yüklemesi için yapılandırılır ("HANA yüklemesi için gerekli" olarak işaretlenir). 
- DR: Veriler, logbackups ve paylaşılan birimler ("depolama çoğaltması" olarak işaretlenen), üretim sitesinden anlık görüntü aracılığıyla çoğaltılır. Bu birimler yalnızca yük devretme zamanı sırasında bağlanır. Daha fazla bilgi için, daha fazla ayrıntı için belge [olağanüstü durum kurtarma yük devretme yordamını](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) okuyun. 
- **SKU türü ı sınıfı** için önyükleme birimi Dr düğümüne çoğaltılır.


## <a name="next-steps"></a>Sonraki adımlar
- HLI için [altyapıyı ve bağlantıyı](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) inceleyin
- HLI için [yüksek kullanılabilirlik ve olağanüstü durum kurtarmayı](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) inceleyin
