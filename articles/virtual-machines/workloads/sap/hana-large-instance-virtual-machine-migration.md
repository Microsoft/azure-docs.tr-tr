---
title: Azure 'da SAP HANA (büyük örnekler) Azure sanal makinelerine geçirme | Microsoft Docs
description: Azure 'da (büyük örnekler) SAP HANA Azure sanal makinelerine geçirme
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd1cfb0cc8e1868e78b4d284d1b1f4e7e85aa318
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677038"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Azure sanal makinelerine Azure büyük örnek geçişi SAP HANA
Bu makalede olası Azure büyük örnek dağıtım senaryoları açıklanmakta ve küçültülmüş geçiş kapalı kalma süresi ile planlama ve geçiş yaklaşımı sunulmaktadır

## <a name="overview"></a>Genel Bakış
Eylül 2016 ' deki Azure büyük örneklerinin SAP HANA (HLI) duyurusu nedeniyle birçok müşteri bu donanımı bellek içi işlem platformu için bir hizmet teklifi olarak benimsemiştir.  Son yıllarda, HANA genişleme dağıtımı desteğiyle birlikte gelen Azure VM boyut uzantısı, kurumsal müşterilerin ERP veritabanı kapasitesi taleplerini en çok aşmış.  SAP HANA iş yüklerini fiziksel sunuculardan Azure VM 'lerine geçirmeyi belirten müşterileri görbaşlayacağız.
Bu kılavuz, adım adım bir yapılandırma belgesi değildir. Ortak dağıtım modellerini açıklar ve planlama ve geçiş önerileri sunar.  Amaç, geçiş kapalı kalma süresini en aza indirme hazırlığı için gerekli noktaları çağırmaktır.

## <a name="assumptions"></a>Varsayımlar
Bu makalede aşağıdaki varsayımlar yapılır:
- Dikkate alınan tek ilgi, Hana büyük örneğinden (HLI) önemli yazılım yükseltme veya düzeltme eki olmadan Azure VM 'ye yönelik homojen bir HANA veritabanı işlem hizmeti geçişidir. Bu küçük güncelleştirmeler, ilgili SAP notları tarafından desteklenen şekilde daha yeni bir işletim sistemi sürümü veya HANA sürümünün kullanımını içerir. 
- Tüm güncelleştirmeler/yükseltmeler etkinliklerinin geçişten önce veya sonra yapılması gerekir.  Örneğin, MCOS SAP HANA MDC dağıtımına dönüştürülüyor. 
- En az kapalı kalma süresini sunan geçiş yaklaşımı sistem çoğaltmasına SAP HANA. Diğer geçiş yöntemleri bu belgenin kapsamının bir parçası değildir.
- Bu kılavuz, HLI 'nin hem Rev3 hem de Rev4 SKU 'Ları için geçerlidir.
- HANA dağıtım mimarisi, geçiş sırasında öncelikle değiştirilmeden kalır.  Diğer bir deyişle, tek örnekli DR içeren bir sistem hedefte aynı şekilde kalır.
- Müşteriler, hedef (e) mimarinin Hizmet Düzeyi Sözleşmesi (SLA) ' ni gözden geçirdiğini ve anlamıştır. 
- HLIs ve VM 'Ler arasındaki ticari terimler farklıdır. Müşteriler, maliyet yönetimi için VM 'lerinin kullanımını izlemelidir.
- Müşteriler, paylaşılan bir işlem platformu olduğunu anladığında, VM 'Ler paylaşılan bir şekilde ayrılmış altyapıda çalışır.
- Müşteriler hedeflenen mimarinizi destekleyen hedef VM 'Leri doğruladı. SAP HANA dağıtımı için sertifikalı tüm desteklenen VM SKU 'Larını görmek için, [SAP HANA donanım dizinine](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)bakın.
- Müşteriler tasarım ve geçiş planını doğruladı.
- Birincil siteyle birlikte olağanüstü durum kurtarma VM 'si için plan yapın.  Müşteriler, geçişten sonra VM 'lerde çalışan birincil site için DR düğümü olarak HLI 'yi kullanamaz.
- Müşteriler gerekli yedekleme dosyalarını, iş kurtarılabilirlik ve uyumluluk gereksinimlerine bağlı olarak hedef VM 'lere kopyaladı. VM erişilebilir yedeklemelerde geçiş süresi boyunca zaman içinde kurtarma yapılmasına izin verir.
- HSR HA için müşterilerin, [SLES](./high-availability-guide-suse-pacemaker.md) ve [rhel](./high-availability-guide-rhel-pacemaker.md)için SAP HANA ha Kılavuzu başına stonith cihazı ayarlaması ve yapılandırması gerekir.  HLI durumu gibi önceden yapılandırılmış değildir.
- Bu geçiş yaklaşımı, Optane yapılandırmasına sahip HLI SKU 'Larını kapsamıyor.

## <a name="deployment-scenarios"></a>Dağıtım senaryoları
HLI müşterilerine sahip ortak dağıtım modelleri aşağıdaki tabloda özetlenmiştir.  Tüm HLI senaryolar için Azure VM 'lerine geçiş mümkündür.  Kullanılabilir tamamlayıcı Azure hizmetlerinden yararlanmak için, düşük mimari değişiklikler gerekebilir.

| Senaryo KIMLIĞI | HLI senaryosu | VM 'ye geçiş mı? | Görüyorum |
| --- | --- | --- | --- |
| 1 | [Tek bir SID içeren tek düğüm](./hana-supported-scenario.md#single-node-with-one-sid) | Yes | - |
| 2 | [MCOS ile tek düğüm](./hana-supported-scenario.md#single-node-mcos) | Yes | - |
| 3 | [Depolama çoğaltması kullanan DR ile tek düğüm](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | No | Depolama çoğaltması Azure sanal platformunda kullanılamaz, geçerli DR çözümünü HSR veya yedekleme/geri yükleme olarak değiştirin |
| 4 | [Depolama çoğaltması kullanarak DR (çok amaçlı) ile tek düğüm](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | No | Depolama çoğaltması Azure sanal platformunda kullanılamaz, geçerli DR çözümünü HSR veya yedekleme/geri yükleme olarak değiştirin |
| 5 | [Yüksek kullanılabilirlik için STONITH ile HSR](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | Yes | Hedef VM 'Ler için önceden yapılandırılmış bir SBD yok.  Bir STONITH çözümü seçin ve dağıtın.  Olası seçenekler: Azure Uçuşlama Aracısı (hem [RHEL](./high-availability-guide-rhel-pacemaker.md), [SLES](./high-availability-guide-suse-pacemaker.md)Için desteklenir), SBD |
| 6 | [HSR ile, depolama çoğaltması ile DR](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | No | DR için depolama çoğaltmasını, HSR veya yedekleme/geri yükleme ile değiştirin |
| 7 | [Konak otomatik yük devretme (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11) | Yes | Azure VM 'leriyle paylaşılan depolama için ANF kullanma |
| 8 | [Bekleme ile genişleme](./hana-supported-scenario.md#scale-out-with-standby) | Yes | Yalnızca depolama için ANF kullanan M128s, M416s, M416ms VM 'Ler ile siyah beyaz/4HANA |
| 9 | [Bekleme olmadan genişleme](./hana-supported-scenario.md#scale-out-without-standby) | Yes | M128s, M416s, M416ms VM 'Leri ile siyah beyaz/4HANA (depolama için ANF kullanma ile veya olmadan) |
| 10 | [Depolama çoğaltması kullanarak DR ile genişleme](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | No | DR için depolama çoğaltmasını, HSR veya yedekleme/geri yükleme ile değiştirin |
| 11 | [HSR kullanarak DR ile tek düğüm](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | Yes | - |
| 12 | [Tek düğümlü HSR-DR (maliyet için iyileştirilmiş)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | Yes | - |
| 13 | [HSR ile HA ve DR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | Yes | - |
| 14 | [HSR ile HA ve DR (maliyet için iyileştirilmiş)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Yes | - |
| 15 | [HSR kullanarak DR ile ölçeklendirme](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | Yes | M128s ile siyah beyaz/4HANA. M416s, M416ms VM 'Leri (depolama için ANF kullanma ile veya olmadan) |


## <a name="source-hli-planning"></a>Kaynak (HLI) planlaması
Bir HLI sunucusu eklerken, hem Microsoft hizmet yönetimi hem de müşterileri, SAP HANA veritabanını çalıştırmaya yönelik işlem, ağ, depolama ve işletim sistemine özgü ayarların planlanmasından geçer.  Azure VM 'ye geçiş için benzer planlama yapılması gerekir.

### <a name="sap-hana-housekeeping"></a>SAP HANA temizlik 
Veritabanı içeriğini istenmeyen, süresi geçmiş veriler veya eski Günlükler yeni veritabanına geçirilmeyecek şekilde almak için iyi bir işletimsel uygulamadır.  Temizlik genellikle eski, son kullanma veya etkin olmayan verileri silme veya arşivlemeyi içerir.  Bu ' veri hygiene ' eylemleri, üretim kullanımından önce veri kırpma geçerliliğini doğrulamak için üretim dışı sistemlerde test edilmelidir.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Yeni VM 'Ler ve sanal ağ için ağ bağlantısına izin ver 
Bir müşterinin HLI dağıtımında, ağ, makale [SAP HANA (büyük örnekler) ağ mimarisi](./hana-network-architecture.md)bölümünde açıklanan bilgilere göre ayarlanmıştır. Ayrıca, ağ trafiği yönlendirme, ' Azure 'da yönlendirme ' bölümünde özetlenen şekilde yapılır.
- Yeni bir VM 'yi geçiş hedefi olarak ayarlama sırasında, IP adresi aralıklarına sahip var olan sanal ağa zaten HLI 'ya bağlanmasına izin verildiğinde, başka bir bağlantı güncelleştirmesi gerekmez.
- Yeni Azure VM yeni bir Microsoft Azure Sanal Ağ yerleştirilmişse, başka bir bölgede olabilir ve var olan sanal ağla eşlenirse, bu yeni sanal ağ IP aralığı için erişime izin vermek üzere özgün HLı sağlama alanındaki ExpressRoute hizmet anahtarı ve kaynak KIMLIĞI kullanılabilir.  Sanal ağın HLI bağlantı kurmasını sağlamak için Microsoft hizmet yönetimiyle koordine edin.  Note: uygulama ve veritabanı katmanları arasındaki ağ gecikmesini en aza indirmek Için hem uygulama hem de veritabanı katmanları aynı sanal ağda olmalıdır.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Mevcut uygulama katmanı kullanılabilirlik kümesi, Kullanılabilirlik Alanları ve yakınlık yerleşimi grubu (PPG)
Geçerli dağıtım modeli, belirli hizmet düzeyi hedeflerini karşılamak için yapılır.  Bu taşımanın hedef altyapının ayarlanan hedefleri karşıladığından veya aştığından emin olun.  
Daha büyük olasılıkla, müşteriler SAP uygulama sunucuları bir kullanılabilirlik kümesine yerleştirilir.  Geçerli dağıtım hizmeti düzeyi tatmin edici ise ve 
- Hedef VM, HLI mantıksal adının ana bilgisayar adını varsaydığı takdirde, VM 'nin IP 'sine işaret eden etki alanı adı hizmeti (DNS) adresi çözümlemenin güncelleştirilmesi, herhangi bir SAP profilini güncelleştirmeden çalışır
- PPG kullanmıyorsanız, ağ gecikmesini en aza indirmek için tüm uygulama ve DB sunucularını aynı bölgeye yerleştirdiğinizden emin olun.
- PPG kullanıyorsanız, bu belgenin bölümüne bakın: ' hedef planlama, kullanılabilirlik kümesi, Kullanılabilirlik Alanları ve yakınlık yerleşimi grubu (PPG) '.

### <a name="storage-replication-discontinuance-process-if-used"></a>Depolama çoğaltması süreksizlik işlemi (kullanılıyorsa)
Depolama çoğaltması DR çözümü olarak kullanılıyorsa, SAP uygulaması kapatıldıktan sonra (zamanlanmamış) sonlandırılmalıdır.  Ayrıca, son SAP HANA Katalog, günlük dosyası ve veri yedeklemeleri, uzak DR HLI depolama birimlerine çoğaltılır.  Bu işlem, fiziksel sunucudan Azure VM 'ye geçiş sırasında bir olağanüstü durum oluşması durumunda önlem olarak yapılır.

### <a name="data-backups-preservation-consideration"></a>Veri yedeklemeleri saklama değerlendirmesi
Azure VM 'de SAP HANA ' den sonra, HLI üzerindeki tüm anlık görüntü tabanlı veriler veya günlük yedeklemeleri, gerektiğinde bir VM 'ye kolayca erişilemez veya geri yüklenebilen. Erken geçiş döneminde, Azure tabanlı yedekleme, zaman içinde kurtarma gereksinimlerini karşılamak için yeterli geçmiş oluşturmadan önce, kesme öncesinde, gün veya hafta anlık görüntülerine ek olarak dosya düzeyinde yedeklemeler yapmanızı öneririz.  Bu yedeklemelerin yeni SAP HANA VM tarafından erişilebilen bir Azure depolama hesabına kopyalanmasını sağlayabilirsiniz.
HLı içeriği yedeklemeye ek olarak, geri alma işleminin gerekli olması durumunda SAP 'nin tam yedeklemelerini güvenli bir şekilde erişilebilir hale gedin.

### <a name="adjusting-system-monitoring"></a>Sistem izlemeyi ayarlama 
Müşteriler, SAP yatay içindeki sistemlere yönelik uyarı bildirimlerini izlemek ve göndermek için birçok farklı araç kullanır.  Bu öğe, izleme değişikliklerini birleştirmek ve gerekirse uyarı bildirim alıcılarını güncelleştirmek için uygun bir eylemin çağrı yaptığı bir çağrıdır.

### <a name="microsoft-operations-team-involvement"></a>Microsoft operasyon ekibi katılımı 
Mevcut HLI örneğine göre Azure portal bir bilet açın.  Destek bileti oluşturulduktan sonra bir destek mühendisi e-postayla sizinle iletişim kuracaktır.  

### <a name="engage-microsoft-account-team"></a>Microsoft hesabı ekibine katılın
İşlem kaynağında masrafın gereksiz kullanımını en aza indirmek için, geçiş, HLI sözleşmesinin yıldönümü yenileme zamanına yakın bir süre sonra planlayın. HLı dikey pencerenin yetkisini almak için, sözleşmenin sonlandırmasını ve birimin gerçek kapatılmasını koordine etmek gerekir.

## <a name="destination-planning"></a>Hedef planlama
Var olan bir altyapının yerini almak için yeni bir altyapının çıkarılması, yeni eklemenin büyük şeyler şemasına sığması için biraz düşünmeye devam eder.  Aşağıda, contemplation için bazı önemli noktaları verilmiştir.

### <a name="resource-availability-in-the-target-region"></a>Hedef bölgede kaynak kullanılabilirliği 
Geçerli SAP uygulama sunucularının dağıtım bölgesi genellikle ilişkili HLIs ile yakın yakınlardır.  Ancak, HLIs kullanılabilir Azure bölgelerinden daha az sayıda konumda sunulur.  Fiziksel HLI 'ı Azure VM 'ye geçirirken, tüm ilgili hizmetlerin performans iyileştirmesi için yakınlık uzaklığı ' ince ayar ' için de iyi bir zaman vardır.  Bu işlemi yaparken, bir anahtar dikkate alınması, seçilen bölgenin tüm gerekli kaynaklara sahip olduğundan emin olunması.  Örneğin, belirli VM ailesinin kullanılabilirliği veya yüksek kullanılabilirlik kurulumu için Azure bölgelerinin sunumu.

### <a name="virtual-network"></a>Sanal ağ 
Müşterilerin yeni HANA veritabanını var olan bir sanal ağda çalıştırıp çalıştırmayacağını veya yeni bir tane oluşturmasını seçmesi gerekir.  Birincil karar verme faktörü, SAP yatay için geçerli ağ yerleşimidir.  Ayrıca, altyapı bir bölgeden iki bölgeler dağıtımına geçtiğinde ve PPG kullanıyorsa, mimari değişiklik uygular. Daha fazla bilgi için [SAP uygulamasıyla en iyi ağ gecikmesi Için Azure PPG](./sap-proximity-placement-scenarios.md)makalesine bakın.   

### <a name="security"></a>Güvenlik
Yeni SAP HANA VM 'nin yeni veya mevcut bir VNET/alt ağ üzerinde giriş yapılıp yapılmayacağını belirtir. Bu, güvenli koruma gerektiren yeni bir iş açısından kritik hizmeti temsil eder.  Bu yeni hizmet sınıfı için değerlendirilmek ve dağıtılması için şirket bilgileri güvenlik ilkesiyle uyumlu erişim denetimi.

### <a name="vm-sizing-recommendation"></a>VM boyutlandırma önerisi
Bu geçiş Ayrıca, HANA işlem motorunuzu doğru boyuta geçirmek için de bir fırsattır.  Bir diğeri, harcama verimliliğini artırmak için doğru boyutlandırmayı sağlayan sistem kaynak tüketimini anlamak için Hana Studio ile birlikte hana [sistem görünümlerini](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) kullanabilir.

### <a name="storage"></a>Depolama 
Depolama performansı, SAP uygulama kullanıcı deneyimini etkileyen faktörlerden biridir.  Belirli bir VM SKU 'SU temel alınarak, [Azure sanal makine depolama yapılandırmalarının SAP HANA](./hana-vm-operations-storage.md)yayımlanan minimum depolama düzeni vardır. Yeni HANA VM için yeterli GÇ kapasitesi ve performans sağlamak üzere, bu en düşük özellikleri gözden geçirdikten ve mevcut HLI sistem istatistiklerine göre karşılaştırılmasını öneririz.

Yeni HANA sanal makinesi ve ilişkili sanal makine için PPG 'yi yapılandırırsanız, depolama ve VM 'nin birlikte bulunması için bir destek bileti göndererek Yedekleme çözümünüzün değiştirilmesi gerektiğinden, işlemsel harcama sürprizleri önlemek için depolama maliyetinin de yeniden ziyaret edilmelidir.

### <a name="storage-replication-for-disaster-recovery"></a>Olağanüstü durum kurtarma için depolama çoğaltma
HLI ile, depolama çoğaltma, olağanüstü durum kurtarma için varsayılan seçenek olarak sunulmuştur. Bu özellik, Azure VM 'de SAP HANA için varsayılan seçenektir. HSR, yedekleme/geri yükleme veya iş gereksinimlerinizi karşılayan diğer desteklenen çözümleri göz önünde bulundurun.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Kullanılabilirlik kümeleri, Kullanılabilirlik Alanları ve yakınlık yerleştirme grupları 
Uygulama katmanı ve SAP HANA arasındaki mesafeyi kısaltmak için, ağ gecikmesini en düşük düzeyde tutmak üzere yeni veritabanı sanal makinesi ve geçerli SAP uygulama sunucuları bir PPG 'ye yerleştirilmelidir. Azure kullanılabilirlik kümesi 'nin ve Kullanılabilirlik Alanları SAP dağıtımları için PPG ile nasıl çalıştığını öğrenmek için [yakınlık yerleşimi grubuna](./sap-proximity-placement-scenarios.md) bakın.
Hedef HANA sisteminin üyeleri birden fazla Azure bölgesinde dağıtılmışsa, müşteriler seçili bölgelerin gecikme süresi profilinin net bir görünümüne sahip olmalıdır. SAP sistem bileşenlerinin yerleştirilmesi, SAP uygulaması ve veritabanı arasındaki uzaklığı en iyi şekilde elde etmek için idealdir.  Genel etki alanı [kullanılabilirlik bölgesi gecikme testi aracı](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) ölçüyü daha kolay hale getirmeye yardımcı olur.  


### <a name="backup-strategy"></a>Yedekleme stratejisi
Birçok müşteri, HLI SAP HANA için üçüncü taraf yedekleme çözümlerini zaten kullanıyor.  Bu durumda yalnızca ek korumalı bir VM ve HANA veritabanlarının yapılandırılması gerekir.  Artık, makinenin geçiş işleminden sonra kullanımdan kalkmakta olması durumunda sürekli olarak devam eden yedekleme işleri planlanmamış olabilir.
VM 'de SAP HANA için Azure Backup genel kullanıma sunulmuştur.  Azure VM 'lerinde [yedekleme](../../../backup/backup-azure-sap-hana-database.md), [geri yükleme](../../../backup/sap-hana-db-restore.md), SAP HANA yedeklemeyi [yönetme](../../../backup/sap-hana-db-manage.md) hakkında ayrıntılı bilgi için şu bağlantılara bakın:

### <a name="dr-strategy"></a>DR stratejisi
Hizmet düzeyi hedefleriniz daha uzun bir kurtarma süresi içeriyorsa, blob depolamaya ve geri yüklemeye yönelik basit bir yedekleme, en basit ve en ucuz DR stratejisidir.  
HANA DR 'nin genellikle HSR ile yapıldığı büyük örnek platformu gibi Azure VM 'de HSR Ayrıca en doğal ve yerel SAP HANA DR çözümüdür.  Kaynak dağıtımının tek örnekli veya kümelenmiş olmasından bağımsız olarak, DR bölgesinde kaynak altyapısının bir kopyası gerekir.
Bu DR çoğaltması, birincil HLI 'dan VM 'ye geçiş tamamlandıktan sonra yapılandırılır.  DR HANA DB, sanal makine örneğindeki birincil SAP HANA ikincil çoğaltma sitesi olarak kaydeder.  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP uygulama sunucusu bağlantısı hedef değişikliği
HSR geçişi yeni bir HANA DB konağına neden olur ve bu nedenle uygulama katmanının yeni bir DB ana bilgisayar adına, SAP profillerinin yeni ana bilgisayar adını yansıtacak şekilde değiştirilmesi gerekir.  Geçiş, ana bilgisayar adını koruyan ad çözümlemesi tarafından yapıldıysa, profil değişikliği gerekli değildir.

### <a name="operating-system"></a>İşletim sistemi
Aynı sürüm düzeyinde olsa da, örneğin, SLES 12 SP4 gibi, HLI ve VM için işletim sistemi görüntüleri aynı değildir. Müşteriler, hedef üzerine paketleri yüklemek için HLI 'da gerekli paketleri, sık düzeltmeler, düzeltme ekleri, çekirdek ve güvenlik düzeltmelerini doğrulamalıdır.  Daha yeni bir işletim sistemi sürümüne sahip bir VM 'ye daha eski bir işletim sistemini çoğaltmak için HSR kullanılması desteklenir.  [SAP note 2763388](https://launchpad.support.sap.com/#/notes/2763388)' i inceleyerek desteklenen belirli sürümleri doğrulayın.

### <a name="new-sap-license-request"></a>Yeni SAP lisans isteği
Yeni HANA sistemi için yeni bir SAP lisansı istemek üzere VM 'lere geçirilme için basit bir çağrı çıkışı.

### <a name="service-level-agreement-sla-differences"></a>Hizmet düzeyi sözleşmesi (SLA) farkları
HLI ve Azure VM arasında kullanılabilirlik SLA 'Sı farkını çağırmak gibi yazarlar.  Örneğin, kümelenmiş HLIs HA çiftleri% 99,99 kullanılabilirlik sağlar. Aynı SLA 'yı başarmak için, birinin kullanılabilirlik bölgelerinde VM 'Leri dağıtması gerekir. Bu [Makale](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) , müşterilerin hedef altyapısını uygun şekilde planlayabilmeleri için ilişkili dağıtım mimarlarıyla kullanılabilirliği açıklar.


## <a name="migration-strategy"></a>Geçiş stratejisi
Bu belgede, HLI 'dan Azure VM 'ye geçiş için yalnızca HANA sistem çoğaltma yaklaşımını ele alınmaktadır.  Dağıtılan hedef depolama çözümüne bağlıdır, işlem biraz farklılık gösterir. Üst düzey adımlar aşağıda açıklanmıştır.

### <a name="vm-with-premiumultra-disks-for-data"></a>Veriler için Premium/Ultra disklere sahip VM
Premium veya ultra disklerle dağıtılan VM 'Ler için, standart SAP HANA sistem çoğaltma yapılandırması HSR 'yi ayarlamak için geçerlidir.  [SAP yardım makalesi](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) , sistem çoğaltmasını ayarlama, ikincil bir sistem üzerinden alma, birinciye geri dönme ve sistem çoğaltmasını devre dışı bırakma ile ilgili adımlara genel bir bakış sağlar.  Geçişin amacı doğrultusunda, yalnızca kurulum, üzerinde işlem yapma ve çoğaltma adımlarını devre dışı bırakma gereksinimimiz olacak.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>Veri ve günlük birimleri için ANF ile VM
Yüksek düzeyde, tam verilerin ve günlük birimlerinin en son HLI depolama anlık görüntülerinin, hedef HANA VM tarafından erişilebilir ve kurtarılabilir oldukları Azure depolama 'ya kopyalanması gerekir.  Kopyalama işlemi, herhangi bir yerel Linux kopyalama araçlarıyla yapılabilir.  

> [!IMPORTANT]
> Kopyalama ve veri aktarımı, HANA veritabanı boyutuna ve ağ bant genişliğine bağlı olarak saat alabilir.  Kopyalama işleminin toplu işlemi, birincil HANA DB kapalı kalma süresinin sonunda yapılmalıdır.

### <a name="mcos-to-mdc-conversion"></a>MCOS-MDC dönüştürmesi
Bir sistem (MCOS) dağıtım modelindeki birden çok bileşen, bazı HLI müşterilerimiz tarafından kullanıldı.  Mosyon, önceki SAP HANA sürümlerinin birden çok veritabanı kapsayıcısı (MDC) depolama anlık görüntü sınırlamasını atlamamıştı.  MCOS modelinde, bazı bağımsız SAP HANA örnekleri tek bir HLI dikey pencerede yığılır.  Geçiş için HSR kullanmak iyi çalışır, ancak tek bir kiracı DB 'ye sahip birden çok HANA VM 'ye neden olur.  Bu son durum, bir müşterinin acözel hale gelmesini sağlayan bir busier Yatayı kolaylaştırır.  SAP HANA 2,0 varsayılan dağıtımının MDC olması sayesinde, HSR geçişinden sonra [Hana kiracı taşıma](https://launchpad.support.sap.com/#/notes/2472478) işlemini gerçekleştirmede uygun bir alternatiftir.  Bu işlem, bu bağımsız HANA veritabanlarını tek bir HANA kapsayıcısında cokiracılar halinde birleştirir.  

### <a name="application-layer-consideration"></a>Uygulama katmanı değerlendirmesi
DB sunucusu bir SAP sisteminin merkezi olarak görüntülenir.  Tüm uygulama sunucuları SAP HANA DB 'nin yakınında bulunmalıdır.  Bazı durumlarda PPG 'nin yeni kullanımı istendiğinde, mevcut uygulama sunucularının HANA VM 'nin gerekli olabileceği PPG üzerine yerini değiştirme.  Dağıtım şablonlarına zaten sahipseniz yeni uygulama sunucularının oluşturulması daha kolay olabilir.  
Mevcut uygulama sunucuları ve yeni HANA VM en iyi şekilde bulunursa, ek kapasite gerekmediği takdirde yeni uygulama sunucularının oluşturulması gerekmez.  
Hizmet kullanılabilirliğini iyileştirmek için yeni bir altyapı oluşturulmazsa, mevcut uygulama sunucuları gereksiz hale gelebilir ve kapatılması ve silinmesi gerekir.
Hedef VM ana bilgisayar adı değiştiyse ve HLI ana bilgisayar adından farklıysa, SAP uygulama sunucusu profillerinin yeni ana bilgisayarı işaret etmek üzere ayarlanması gerekir.  Yalnızca HANA DB IP adresi değiştiyse, yeni HANA VM 'sine gelen bağlantıları sağlamak için bir DNS kaydı güncelleştirmesi gerekir.

### <a name="acceptance-test"></a>Kabul testi
HLI 'dan VM 'ye geçiş, heterojen bir geçişe kıyasla veritabanı içeriğine hiçbir malzeme değişikliği yapmaz, ancak yeni kurulumun önemli işlevlerini ve performans oranını doğrulamanızı öneririz.  

### <a name="cutover-plan"></a>Cutover planı
Bu geçiş doğrudan ileri doğru olsa da, var olan bir veritabanının yetkisini almayı içerir.  Kaynak sistemi ilişkili içeriğiyle korumak için dikkatli planlama ve geri dönüş gerekli olduğunda yedekleme görüntüleri kritik öneme sahiptir.  İyi planlama, bir spedıma ters değişikliği sağlar.   


## <a name="post-migration"></a>Geçiş sonrası
Veri bütünlüğünün korunduğundan emin olmak için, herhangi bir HLI bağımlı hizmeti veya bağlantıyı güvenli bir şekilde ayırdığımızda geçiş işi yapılmaz.  Ayrıca, gereksiz hizmetleri kapatın.  Bu bölüm, birkaç önemli öğeyi çağırır.

### <a name="decommissioning-the-hli"></a>HLI 'nın yetkisini alma
HANA DB 'nin Azure VM 'ye başarılı bir şekilde geçişinden sonra, hiçbir üretken iş işleminin HLI DB üzerinde çalıştırılkullanılmadığından emin olun.  Ancak, HLI 'nın çalışır durumda olduğu bir süre için yerel yedekleme saklama penceresine eşit tutulması, gerektiğinde kurtarma kurtarması sağlayan güvenli bir uygulamadır.  Yalnızca, HLı dikey pencerenin kullanımdan alınması gerekir.  Müşteriler, Microsoft temsilcileriyle iletişim kurarak HLI taahhütlerini Microsoft ile sonuçlandırma sözleşmeye dayalı gerekir.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>HLI için yapılandırılmış tüm proxy 'leri (örn. Iptables, BIGIP) kaldırın 
Şirket içi trafiği HLI 'ya yönlendirmek için Iptables gibi bir proxy hizmeti kullanılıyorsa, sanal makineye başarılı geçişten sonra artık gerekli değildir.  Ancak, bu bağlantı hizmeti, HLı dikey pencerenin hala bulunduğu sürece tutulmalıdır.  Yalnızca HLI dikey penceresinden tamamen kullanımdan kaldırıldıktan sonra hizmeti kapatın.

### <a name="remove-global-reach-for-hli"></a>HLI için Global Reach kaldır 
Global Reach, müşterilerin ExpressRoute ağ geçidini HLI ExpressRoute ağ geçidiyle bağlamak için kullanılır.  Müşterilerin Şirket içi trafiğinin, proxy hizmeti kullanılmadan doğrudan HLI kiracısına ulaşmasını sağlar.  Bu bağlantı, geçişten sonra HLI birimi yokluğunda artık gerekli değildir.  Iptables proxy hizmeti gibi, HLı dikey pencere tam olarak kullanımdan kaldırılana kadar GlobalReach da tutulmalıdır.

### <a name="operating-system-subscription--movereuse"></a>İşletim sistemi aboneliği – taşı/yeniden kullan
VM sunucuları Stood ve HLI Blade 'ler kullanımdan kaldırılarak, işletim sistemi abonelikleri, iki farklı işletim sistemi lisansı ödemesinden kaçınmak için değiştirilebilir veya yeniden kullanılabilir.



## <a name="next-steps"></a>Sonraki adımlar
Şu makalelere bakın:
- [Azure 'da altyapı yapılandırma ve işlemlerini SAP HANA](./hana-vm-operations.md).
- [Azure 'Da SAP iş yükleri: planlama ve dağıtım denetim listesi](./sap-deployment-checklist.md).
