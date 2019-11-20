---
title: Azure geçişi sunucu değerlendirmesi ile değerlendirme oluşturmak için en iyi uygulamalar
description: Azure geçişi sunucu değerlendirmesi ile değerlendirmeler oluşturmaya yönelik ipuçları sağlar.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: raynew
ms.openlocfilehash: e235116ab77159a0e2e9c66ad09cdb86ce6da1e9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466937"
---
# <a name="best-practices-for-creating-assessments"></a>Değerlendirme oluşturmak için en iyi uygulamalar

[Azure geçişi](migrate-overview.md) , Microsoft Azure için uygulamaları, altyapıyı ve iş yüklerini keşfetmenize, değerlendirmenize ve geçirmenize yardımcı olan araçların merkezini sağlar. Hub, Azure geçiş araçları ve üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini içerir.

Bu makalede, Azure geçişi sunucu değerlendirmesi Aracı kullanılarak değerlendirmeler oluşturulurken en iyi yöntemler özetlenmektedir.

## <a name="about-assessments"></a>Değerlendirmeler hakkında

Azure geçişi sunucu değerlendirmesi ile oluşturduğunuz değerlendirmeler, verilerin bir zaman noktası anlık görüntüsüdür. Azure geçişi 'nde iki tür değerlendirme vardır.

**Değerlendirme türü** | **Ayrıntılar** | **Veriler**
--- | --- | ---
**Performans tabanlı** | Toplanan performans verilerine dayalı öneriler oluşturan değerlendirmeler | VM boyutu önerisi, CPU ve bellek kullanımı verilerine göre belirlenir.<br/><br/> Disk türü önerisi (Standart HDD/SSD veya Premium yönetilen diskler), şirket içi disklerin ıOPS ve aktarım hızını temel alır.
**Şirket içi olarak** | Öneriler oluşturmak için performans verilerini kullanmayan değerlendirmeler. | VM boyutu önerisi, şirket içi VM boyutunu temel alır<br/><br> Önerilen disk türü, değerlendirme için depolama türü ayarında neleri seçdiklerinize bağlıdır.

### <a name="example"></a>Örnek
Örnek olarak, %20 ' de dört çekirdekli bir şirket içi sanal makine ve %10 kullanımı ile 8 GB bellek varsa, değerlendirmeler aşağıdaki gibi olacaktır:

- **Performans tabanlı değerlendirme**:
    - Core (4 x 0,20 = 0,8) ve bellek (8 GB x 0,10 = 0,8) kullanımı temel alınarak etkin çekirdekleri ve bellek tanımlar.
    - Boyutlandırma için kullanılacak değerleri almak için değerlendirme özelliklerinde belirtilen rahatlık faktörünü (diyelim ki 1,3 x) uygular. 
    - , Azure 'da ~ 1,04 çekirdek (0,8 x 1,3) ve ~ 1,04 GB (0,8 x 1,3) belleği destekleyebilen en yakın VM boyutunu önerir.

- **As (Şirket içi) değerlendirmesi**:
    -  Dört çekirdekli bir VM önerir; 8 GB bellek.

## <a name="best-practices-for-creating-assessments"></a>Değerlendirme oluşturmak için en iyi uygulamalar

Azure geçişi gereci, şirket içi ortamınızı sürekli olarak profiller ve meta verileri ve performans verilerini Azure 'a gönderir. Bir gereç kullanılarak bulunan sunucuların değerlendirmelerinde bu en iyi uygulamaları izleyin:

- Bir **as-to değerlendirmesi oluştur**: makineleriniz Azure geçişi portalında görünür.
- **Performans tabanlı değerlendirme oluşturma**: bulmayı ayarladıktan sonra, performans tabanlı bir değerlendirmeyi çalıştırmadan önce en az bir gün beklemeniz önerilir:
    - Performans verilerinin toplanması zaman alır. En az bir günün beklenmesi, değerlendirmeyi çalıştırmadan önce yeterli performans veri noktası olmasını sağlar.
    - Performans tabanlı değerlendirmeler çalıştırırken ortamınızı değerlendirme süresi boyunca profillediğinizden emin olun. Örneğin, bir haftalık olarak ayarlanan performans süresiyle bir değerlendirme oluşturursanız, tüm veri noktalarının toplanması için bulmayı başlattıktan sonra en az bir hafta beklemeniz gerekir. Aksi takdirde, değerlendirme beş yıldızlı bir derecelendirme almaz.
- **Değerlendirmeleri yeniden hesapla**: Değerlendirmeler, zaman içinde anlık görüntüler olduğundan, en son verilerle otomatik olarak güncellenmez. En son verilerle bir değerlendirmeyi güncelleştirmek için yeniden hesaplamanız gerekir.

Azure geçişi ile içeri aktarılan sunucuların değerlendirmesi için bu en iyi uygulamaları izleyin. CSV dosyası:

- Bir **as-to değerlendirmesi oluştur**: makineleriniz Azure geçişi portalında görünür.
- **Performans tabanlı değerlendirme oluştur**: Bu, özellikle de şirket içinde fazla sağlanmış sunucu kapasiteniz varsa daha iyi maliyet tahmini sağlamaya yardımcı olur. Ancak, performans tabanlı değerlendirmenin doğruluğu, sunucular için sizin tarafınızdan belirtilen performans verilerine bağlıdır. 
- **Değerlendirmeleri yeniden hesapla**: Değerlendirmeler, zaman içinde anlık görüntüler olduğundan, en son verilerle otomatik olarak güncellenmez. En son içeri aktarılan verilerle bir değerlendirmeyi güncelleştirmek için yeniden hesaplamanız gerekir.

## <a name="best-practices-for-confidence-ratings"></a>Güvenirlik derecelendirmeleri için en iyi uygulamalar

Performans tabanlı değerlendirmeler çalıştırdığınızda, değerlendirmeye 1-yıldız (en düşük) ile 5 yıldız (en yüksek) arasında bir güvenilirlik derecesi verilir. Güvenilirlik derecelendirmelerini etkin bir şekilde kullanmak için:
- Azure geçişi sunucu değerlendirmesi VM CPU 'SU/belleği için kullanım verilerine ihtiyaç duyuyor.
- Şirket içi VM 'ye bağlı her disk için okuma/yazma ıOPS/aktarım hızı verileri gerekir.
- SANAL makineye bağlı her ağ bağdaştırıcısı için ağ/çıkış verileri gerekir.

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

Bir değerlendirme oluşturduktan sonra bir gruba makine ekler veya kaldırırsanız, oluşturduğunuz değerlendirme **eşitlenmemiş**olarak işaretlenir. Grup değişikliklerini yansıtmak için değerlendirmeyi yeniden çalıştırın (**yeniden hesapla**).

### <a name="outdated-assessments"></a>Güncel olmayan değerlendirmeler

Değerlendirilen bir gruptaki VM 'lerde şirket içi değişiklikler varsa, değerlendirme **güncelliğini yitirmiş**olarak işaretlenir. Değişiklikleri yansıtmak için değerlendirmeyi yeniden çalıştırın.

### <a name="low-confidence-rating"></a>Düşük güvenilirlikli derecelendirme

Bir değerlendirme, birkaç nedenden dolayı tüm veri noktalarına sahip olmayabilir:

- Değerlendirmeyi oluşturduğunuz süre boyunca ortamınızın profilini oluşturmadınız. Örneğin, performans süresi bir hafta olarak ayarlanan *performans tabanlı bir değerlendirme* oluşturuyorsanız, toplanan tüm veri noktaları için bulmayı başlattıktan sonra en az bir hafta beklemeniz gerekir. En son geçerli güvenilirlik derecelendirmesini görmek için her zaman **yeniden hesapla** ' yı tıklayabilirsiniz. Güvenilirlik derecelendirmesi yalnızca *performans tabanlı* bir değerlendirme oluşturduğunuzda uygulanabilir.

- Değerlendirmenin hesaplandığı dönem boyunca birkaç sanal makine kapatılmıştır. Bazı VM'ler belirli bir süre boyunca kapatıldıysa Sunucu Değerlendirmesi o süreye ait performans verilerini toplayamaz.

- Sunucu Değerlendirmesi'nde bulma işlemi başlatıldıktan sonra birkaç VM oluşturulmuştur. Örneğin, son bir ayın performans geçmişi için değerlendirme oluşturuyorsanız, ancak yalnızca bir hafta önce ortamda birkaç sanal makine oluşturulduysa. Bu durumda, sürenin tamamında yeni VM'lerin performans verileri sağlanmaz ve güvenilirlik derecesi düşük olabilir.


## <a name="next-steps"></a>Sonraki adımlar

- Değerlendirmelerin nasıl hesaplanacağını [öğrenin](concepts-assessment-calculation.md) .
- Bir değerlendirmeyi özelleştirmeyi [öğrenin](how-to-modify-assessment.md) .
