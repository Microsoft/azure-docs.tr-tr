---
title: Azure geçişi bulma ve değerlendirme aracında en iyi değerlendirme uygulamaları
description: Azure geçişi bulma ve değerlendirme aracı ile değerlendirmeler oluşturmaya yönelik ipuçları.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: fac488ba1881b6b79139eaf2468237e546737177
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077339"
---
# <a name="best-practices-for-creating-assessments"></a>Değerlendirme oluşturmak için en iyi uygulamalar

[Azure geçişi](./migrate-services-overview.md) , Microsoft Azure için uygulamaları, altyapıyı ve iş yüklerini keşfetmenize, değerlendirmenize ve geçirmenize yardımcı olan araçların merkezini sağlar. Hub, Azure geçiş araçları ve üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini içerir.

Bu makalede, Azure geçişi bulma ve değerlendirme aracı kullanılarak değerlendirmeler oluşturulurken en iyi yöntemler özetlenmektedir.

Azure geçişi ile oluşturduğunuz değerlendirmeler: bulma ve değerlendirme aracı, verilerin bir zaman noktası anlık görüntüsüdür. Azure geçişi: bulma ve değerlendirme kullanarak oluşturabileceğiniz üç tür değerlendirme vardır:

**Değerlendirme Türü** | **Ayrıntılar**
--- | --- 
**Azure VM** | Şirket içi sunucularınızı Azure sanal makinelerine geçirmeye yönelik değerlendirmeler. <br/><br/> Bu değerlendirme türünü kullanarak, [VMware](how-to-set-up-appliance-vmware.md) ve [Hyper-V](how-to-set-up-appliance-hyper-v.md) ortamındaki şirket içi sunucularınızı ve Azure 'a geçiş için [fiziksel sunucuları](how-to-set-up-appliance-physical.md) değerlendirebilirsiniz. [Daha fazla bilgi edinin](concepts-assessment-calculation.md)
**Azure SQL** | Şirket içi SQL Server 'larınızı VMware ortamınızdan Azure SQL veritabanı 'na veya Azure SQL yönetilen örneğine geçirmeye yönelik değerlendirmeler. [Daha Fazla Bilgi](concepts-azure-sql-assessment-calculation.md)
**Azure VMware Çözümü (AVS)** | Şirket içi sunucularınızı [Azure VMware Çözümü'ne (AVS)](../azure-vmware/introduction.md) geçirmeye yönelik değerlendirmeler. <br/><br/> Bu değerlendirme türünü kullanarak Azure VMware çözümüne (AVS) geçiş için şirket içi [VMware VM](how-to-set-up-appliance-vmware.md) 'lerinizi değerlendirebilirsiniz. [Daha fazla bilgi edinin](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Bulma ve değerlendirme aracında Azure VM veya AVS değerlendirmesi sayısı yanlışsa, Tüm değerlendirmelere gitmek ve Azure VM veya AVS değerlendirmelerini yeniden hesaplamak için toplam değerlendirme sayısına tıklayın. Bulma ve değerlendirme aracı daha sonra bu değerlendirme türü için doğru sayıyı gösterir. 

### <a name="sizing-criteria"></a>Boyutlandırma ölçütü
Azure geçiş değerlendirmelerinde boyutlandırma ölçütü seçenekleri:

**Boyutlandırma ölçütü** | **Ayrıntılar** | **Veriler**
--- | --- | ---
**Performans tabanlı** | Toplanan performans verilerine göre öneriler sunan değerlendirmeler | **Azure VM değerlendirmesi**: VM boyutu önerisi, CPU ve bellek kullanımı verilerini dikkate alır.<br/><br/> Disk türü önerisi (standart HDD/SSD veya premium yönetilen diskler), şirket içi ortamda bulunan disklerin IOPS ve aktarım hızı verilerini dikkate alır.<br/><br/>**Azure SQL değerlendirmesi**: Azure SQL YAPıLANDıRMASı; CPU kullanımı, bellek kullanımı, IOPS (veri ve günlük dosyaları), aktarım hızı ve GÇ işlemlerinin gecikme SÜRESINI içeren SQL örnekleri ve veritabanlarının performans verilerini temel alır.<br/><br/>**Azure VMware Çözümü (AVS) değerlendirmesi**: AVS düğümü önerisi, CPU ve bellek kullanımı verilerini dikkate alır.
**Şirket içinde olduğu gibi** | Bu değerlendirmeler, öneride bulunmak için performans verilerini kullanmaz. | **Azure VM değerlendirmesi**: VM boyutu önerisi, şirket içi VM boyutunu temel alır<br/><br> Önerilen disk türü, değerlendirme için belirlediğiniz depolama türü ayarına bağlıdır.<br/><br/> **Azure VMware Çözümü (AVS) değerlendirmesi**: AVS düğümü önerisi, şirket içi VM boyutunu temel alır.

#### <a name="example"></a>Örnek
Örnek olarak, %20 ' de dört çekirdekli bir şirket içi sanal makine ve %10 kullanımı ile 8 GB bellek varsa, Azure VM değerlendirmesi aşağıdaki gibi olacaktır:

- **Performans tabanlı değerlendirme**:
    - Core (4 x 0,20 = 0,8) ve bellek (8 GB x 0,10 = 0,8) kullanımı temel alınarak etkin çekirdekleri ve bellek tanımlar.
    - Boyutlandırma için kullanılacak değerleri almak için değerlendirme özelliklerinde belirtilen rahatlık faktörünü (diyelim ki 1,3 x) uygular. 
    - , Azure 'da ~ 1,04 çekirdek (0,8 x 1,3) ve ~ 1,04 GB (0,8 x 1,3) belleği destekleyebilen en yakın VM boyutunu önerir.

- **As (Şirket içi) değerlendirmesi**:
    -  Dört çekirdekli bir VM önerir; 8 GB bellek.


## <a name="best-practices-for-creating-assessments"></a>Değerlendirme oluşturmak için en iyi uygulamalar

Azure geçişi gereci, şirket içi ortamınızı sürekli olarak profiller ve meta verileri ve performans verilerini Azure 'a gönderir. Bir gereç kullanılarak bulunan sunucuların değerlendirmelerinde bu en iyi uygulamaları izleyin:

- Bir **as-to değerlendirmesi oluştur**: sunucularınız Azure geçişi portalında gösterildikten sonra as-to değerlendirmesi oluşturabilirsiniz. Boyutlandırma ölçütleriyle "Şirket içi olarak" bir Azure SQL değerlendirmesi oluşturamazsınız.
- **Performans tabanlı değerlendirme oluşturma**: bulmayı ayarladıktan sonra, performans tabanlı bir değerlendirmeyi çalıştırmadan önce en az bir gün beklemeniz önerilir:
    - Performans verilerinin toplanması zaman alır. En az bir günün beklenmesi, değerlendirmeyi çalıştırmadan önce yeterli performans veri noktası olmasını sağlar.
    - Performans tabanlı değerlendirmeler çalıştırırken ortamınızı değerlendirme süresi boyunca profillediğinizden emin olun. Örneğin, bir haftalık olarak ayarlanan performans süresiyle bir değerlendirme oluşturursanız, tüm veri noktalarının toplanması için bulmayı başlattıktan sonra en az bir hafta beklemeniz gerekir. Aksi takdirde, değerlendirme beş yıldızlı bir derecelendirme almaz.
- **Değerlendirmeleri yeniden hesapla**: Değerlendirmeler, zaman içinde anlık görüntüler olduğundan, en son verilerle otomatik olarak güncellenmez. En son verilerle bir değerlendirmeyi güncelleştirmek için yeniden hesaplamanız gerekir.

Azure geçişi ile içeri aktarılan sunucuların değerlendirmesi için bu en iyi uygulamaları izleyin. CSV dosyası:

- Bir **as-to değerlendirmesi oluştur**: sunucularınız Azure geçişi portalında gösterildikten sonra as-to değerlendirmesi oluşturabilirsiniz.
- **Performans tabanlı değerlendirme oluştur**: Bu, özellikle de şirket içinde fazla sağlanmış sunucu kapasiteniz varsa daha iyi maliyet tahmini sağlamaya yardımcı olur. Ancak, performans tabanlı değerlendirmenin doğruluğu, sunucular için sizin tarafınızdan belirtilen performans verilerine bağlıdır. 
- **Değerlendirmeleri yeniden hesapla**: Değerlendirmeler, zaman içinde anlık görüntüler olduğundan, en son verilerle otomatik olarak güncellenmez. En son içeri aktarılan verilerle bir değerlendirmeyi güncelleştirmek için yeniden hesaplamanız gerekir.
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>AVS değerlendirmesi için FTT boyutlandırma parametreleri

AVS 'de kullanılan depolama altyapısı vSAN ' dır. vSAN depolama ilkeleri sanal makineleriniz için depolama gereksinimlerini tanımlar. Bu ilkeler sanal makineler için gerekli hizmet düzeyini garanti eder çünkü depolamanın sanal makineye nasıl ayrıldığını belirler. Kullanılabilir FTT-Raid Bileşimleri şunlardır: 

**Tolerans Hataları (FTT)** | **RAID Yapılandırması** | **Gereken Minimum Konak Sayısı** | **Boyutlandırmada dikkat edilmesi gerekenler**
--- | --- | --- | --- 
1 | RAID-1 (Yansıtma) | 3 | 100 GB sanal makine 200 GB kullanır.
1 | RAID-5 (Silinme Kodlaması) | 4 | 100 GB sanal makine 133,33 GB kullanır
2 | RAID-1 (Yansıtma) | 5 | 100 GB sanal makine 300 GB kullanır.
2 | RAID-6 (Silinme Kodlaması) | 6 | 100 GB sanal makine 150 GB kullanır.
3 | RAID-1 (Yansıtma) | 7 | 100 GB sanal makine 400 GB kullanır.


## <a name="best-practices-for-confidence-ratings"></a>Güvenirlik derecelendirmeleri için en iyi uygulamalar

Performans tabanlı değerlendirmeler çalıştırdığınızda, değerlendirmeye 1-yıldız (en düşük) ile 5 yıldız (en yüksek) arasında bir güvenilirlik derecesi verilir. Güvenilirlik derecelendirmelerini etkin bir şekilde kullanmak için:

- Azure VM ve AVS değerlendirmelerinde şunlar gerekir:
    - Sunucuların her biri için CPU ve bellek kullanımı verileri
    - Şirket içi sunucuya bağlı her bir disk için okuma/yazma ıOPS/işleme verileri
    - Sunucuya bağlı her ağ bağdaştırıcısı için ağ/çıkış verileri.
     
- Azure SQL değerlendirmelerinde, SQL örneklerinin ve değerlendirilmekte olan veritabanlarının performans verileri gerekir ve bunlar şunları içerir:
    - CPU ve bellek kullanımı verileri
    - Verilerin ve günlük dosyalarının okuma/yazma ıOPS/aktarım hızı verileri
    - GÇ işlemlerinin gecikmesi

Seçilen süre için kullanılabilir olan veri noktalarının yüzdesine bağlı olarak, bir değerlendirmenin güvenilirlik derecelendirmesi aşağıdaki tabloda özetlenen şekilde sağlanır.

   **Veri noktası kullanılabilirliği** | **Güvenilirlik derecelendirmesi**
   --- | ---
   %0-%20 | 1 Yıldız
   %21-%40 | 2 Yıldız
   %41-%60 | 3 Yıldız
   %61-%80 | 4 Yıldız
   %81-%100 | 5 Yıldız


## <a name="common-assessment-issues"></a>Ortak değerlendirme sorunları

Değerlendirmeleri etkileyen bazı yaygın ortam sorunlarının nasıl ele alınacağını aşağıda bulabilirsiniz.

###  <a name="out-of-sync-assessments"></a>Eşitleme dışı değerlendirmeler

Bir değerlendirme oluşturduktan sonra bir gruba sunucu ekler veya kaldırırsanız, oluşturduğunuz değerlendirme **eşitlenmemiş** olarak işaretlenir. Grup değişikliklerini yansıtmak için değerlendirmeyi yeniden çalıştırın (**yeniden hesapla**).

### <a name="outdated-assessments"></a>Güncel olmayan değerlendirmeler

#### <a name="azure-vm-assessment-and-avs-assessment"></a>Azure VM değerlendirmesi ve AVS değerlendirmesi
Değerlendirilen bir gruptaki şirket içi sunucularda değişiklikler varsa, değerlendirme **süresi geçmiş** olarak işaretlenir. Aşağıdaki özelliklerde bir veya daha fazla değişiklik olduğundan, bir değerlendirme "güncel değil" olarak işaretlenebilir:
- İşlemci çekirdekleri sayısı
- Ayrılan bellek
- Önyükleme türü veya bellenim
- İşletim sistemi adı, sürümü ve mimarisi
- Disk sayısı
- Ağ bağdaştırıcısı sayısı
- Disk boyutu değişikliği (GB ayrılmış)
- NIC özellikleri güncelleştirmesi. Örnek: MAC adresi değişiklikleri, IP adresi ekleme vb.
    
Değişiklikleri yansıtmak için değerlendirmeyi yeniden çalıştırın (yeniden **Hesapla**).
    
#### <a name="azure-sql-assessment"></a>Azure SQL değerlendirmesi
Üzerinde değerlendirilen bir gruptaki şirket içi SQL örneklerinde ve veritabanlarında değişiklikler varsa, değerlendirme **süresi geçmiş** olarak işaretlenir. Aşağıdaki nedenlerden biri veya birkaçı nedeniyle bir değerlendirme "tarihi geçmiş" olarak işaretlenebilir:
- Sunucuda SQL örneği eklendi veya kaldırıldı
- SQL örneğinde SQL veritabanı eklendi veya kaldırıldı
- SQL örneğindeki toplam veritabanı boyutu %20'den fazla değiştirildi
- İşlemci çekirdekleri sayısı değişikliği
- Ayrılmış bellekte değişiklik        
  
    Değişiklikleri yansıtmak için değerlendirmeyi yeniden çalıştırın (yeniden **Hesapla**).

### <a name="low-confidence-rating"></a>Düşük güvenilirlikli derecelendirme

Bir değerlendirme, birkaç nedenden dolayı tüm veri noktalarına sahip olmayabilir:

- Değerlendirmeyi oluşturduğunuz süre boyunca ortamınızın profilini oluşturmadınız. Örneğin değerlendirmeyi bir hafta olarak ayarlanmış performans süresiyle oluşturuyorsanız, toplanacak tüm veri noktalarının keşfini başlattıktan sonra en az bir hafta beklemeniz gerekir. Süreyi bekleyemiyorsanız, performans süresini daha kısa olacak şekilde değiştirin ve değerlendirmeyi 'Yeniden Hesaplayın'.
 
- Değerlendirme, değerlendirme süresi içinde sunucuların bir bölümü veya tümü için performans verilerini toplayamıyor. Yüksek güvenilirlikli bir derecelendirme için lütfen şunları doğrulayın: 
    - Değerlendirme süresi boyunca sunucular açık
    - 443 bağlantı noktalarında giden bağlantılara izin verilir
    - Hyper-V sunucuları için dinamik bellek etkin 
    - Azure geçişi 'ndeki aracıların bağlantı durumu ' Connected ' ve son sinyali denetle
    - Azure SQL değerlendirmelerinde, tüm SQL örnekleri için Azure geçişi bağlantı durumu bulunan SQL örneği dikey penceresinde "bağlandı" olarak ayarlanır

    Güvenilirlik derecelendirmesindeki en son değişiklikleri yansıtacak şekilde değerlendirmeyi 'Yeniden Hesaplayın'.

- Azure VM ve AVS değerlendirmelerinde, bulma başlatıldıktan sonra birkaç sunucu oluşturulmuştur. Örneğin, son bir ayın performans geçmişi için bir değerlendirme oluşturuyorsanız ancak ortamda yalnızca bir hafta önce birkaç sunucu oluşturuluyorsa. Bu durumda, yeni sunucular için performans verileri sürenin tamamına uygun olmayacaktır ve güvenirlik derecelendirmesi düşük olacaktır.

- Azure SQL değerlendirmelerinde, bulma başlatıldıktan sonra birkaç SQL örneği veya veritabanı oluşturuldu. Örneğin, son bir ayın performans geçmişi için bir değerlendirme oluşturuyorsanız ancak ortamda yalnızca bir hafta önce birkaç SQL örneği veya veritabanı oluşturuluyorsa. Bu durumda, yeni sunucular için performans verileri sürenin tamamına uygun olmayacaktır ve güvenirlik derecelendirmesi düşük olacaktır.

### <a name="migration-tool-guidance-for-avs-assessments"></a>AVS değerlendirmesi için geçiş aracı Kılavuzu

Azure VMware Çözümü (AVS) değerlendirmesinin Azure için hazır olma raporunda aşağıdaki önerilen araçları görebilirsiniz: 
- VMware **HCX veya Enterprise**: VMware sunucuları Için VMware karma bulut uzantısı (HCX) çözümü, şirket içi iş yükünüzü Azure VMware çözümünüz (AVS) özel bulutuna geçirmek için önerilen geçiş aracıdır. [Daha Fazla Bilgi Edinin](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Bilinmiyor**: CSV dosyası aracılığıyla içeri aktarılan sunucular için varsayılan geçiş aracı bilinmez. Ancak, VMware ortamındaki sunucular için, VMware karma bulut uzantısı (HCX) çözümünün kullanılması önerilir.


## <a name="next-steps"></a>Sonraki adımlar

- Değerlendirmelerin nasıl hesaplanacağını [öğrenin](concepts-assessment-calculation.md) .
- Bir değerlendirmeyi özelleştirmeyi [öğrenin](how-to-modify-assessment.md) .
