---
title: Azure sunucu değerlendirmesi geçişi için değerlendirmeleri özelleştirin | Microsoft Docs
description: Azure geçişi sunucu değerlendirmesi ile oluşturulan değerlendirmelerin nasıl özelleştirileceğini açıklar
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234280"
---
# <a name="customize-an-assessment"></a>Bir değerlendirmeyi özelleştirme

Bu makalede, Azure geçişi sunucu değerlendirmesi tarafından oluşturulan değerlendirmelerin nasıl özelleştirileceği açıklanır.

[Azure geçişi](migrate-services-overview.md) , şirket içi uygulamalarınızı, iş yüklerinizi ve özel/genel bulut VM 'lerini Azure 'a bulmayı, değerlendirmeyi ve geçirmeyi izlemek için bir merkezi Merkez sağlar. Hub, değerlendirme ve geçiş için Azure geçiş araçları ve ayrıca üçüncü taraf bağımsız yazılım satıcısı (ISV) teklifleri sağlar.

Azure 'a geçiş için hazırlık bölümünde şirket içi VMware VM 'leri ve Hyper-V VM 'Leri için değerlendirmeler oluşturmak üzere Azure geçişi sunucu değerlendirmesi aracını kullanabilirsiniz.

## <a name="about-assessments"></a>Değerlendirmeler hakkında

Azure geçişi sunucu değerlendirmesi kullanarak çalıştırabileceğiniz iki tür değerlendirme vardır.

**Değerlendirmesini** | **Ayrıntılar** | **Veri**
--- | --- | ---
**Performans tabanlı** | Toplanan performans verilerine dayalı değerlendirmeler | **ÖNERILEN VM boyutu**: CPU ve bellek kullanımı verilerine göre.<br/><br/> **Önerilen disk türü (Standart veya Premium yönetilen disk)** : Şirket içi disklerin ıOPS ve aktarım hızına göre.
**Şirket içi olarak** | Şirket içi boyutlandırmayı temel alan değerlendirmeler. | **ÖNERILEN VM boyutu**: Şirket içi VM boyutuna bağlı olarak<br/><br> **Önerilen disk türü**: Değerlendirme için seçtiğiniz depolama türü ayarına göre.


## <a name="how-is-an-assessment-done"></a>Değerlendirme nasıl yapılır?

Azure geçişi sunucu değerlendirmesi sırasında yapılan bir değerlendirme üç aşamaya sahiptir. Değerlendirme, bir uygunluk analizi, ardından boyutlandırma ve son olarak aylık maliyet tahmini ile başlar. Bir makine, bir öncekini geçerse daha sonraki bir aşamaya geçer. Örneğin, bir makine Azure uygunluk denetiminde başarısız olursa, Azure için uygun değil olarak işaretlenir ve boyutlandırma ve maliyetlendirme yapılmaz. [Daha fazla bilgi edinin.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Bir değerlendirme neleri içerir?

**Özellik** | **Ayrıntılar**
--- | ---
**Hedef konum** | Geçişi yapmak istediğiniz Azure konumu.<br/> Sunucu değerlendirmesi Şu anda bu hedef bölgeleri destekliyor: Avustralya Doğu, Avustralya Güneydoğu, Brezilya Güney, Kanada Orta, Kanada Doğu, Orta Hindistan, Orta ABD, Çin Doğu, Çin Kuzey, Doğu Asya, Doğu ABD, Doğu ABD2, Almanya Orta, Almanya Kuzeydoğu, Japonya Doğu, Japonya Batı, Kore Orta, Kore Güney, Kuzey Orta ABD, Kuzey Avrupa, Orta Güney ABD, Güneydoğu Asya, Güney Hindistan, UK Güney, UK Batı, US Gov Arizona, US Gov Teksas, US Gov Virginia, Orta Batı ABD, Batı Avrupa, Batı Hindistan, Batı ABD ve Batı ABD2.
**Depolama türü** | Azure 'da, ' ye taşımak istediğiniz disk türünü belirtmek için bu özelliği kullanabilirsiniz.<br/><br/> Şirket içi olarak boyutlandırma için, hedef depolama türünü Premium yönetilen diskler, Standart SSD yönetilen diskler veya Standart HDD tarafından yönetilen diskler olarak belirtebilirsiniz. Performans tabanlı boyutlandırma için, hedef disk türünü otomatik, Premium yönetilen diskler, Standart HDD yönetilen diskler veya Standart SSD tarafından yönetilen diskler olarak belirtebilirsiniz.<br/><br/> Depolama türünü otomatik olarak belirttiğinizde, disk önerisi, disklerin performans verilerine (ıOPS ve aktarım hızı) göre yapılır. Depolama türünü Premium/standart olarak belirtirseniz, değerlendirme, seçilen depolama türü içinde bir disk SKU 'SU önerir. % 99,9 tek örnekli bir VM SLA 'Sı elde etmek istiyorsanız, depolama türünü Premium ile yönetilen diskler olarak belirtmek isteyebilirsiniz. Bu, değerlendirmede tüm disklerin Premium tarafından yönetilen diskler olarak önerilmesini sağlar. Azure
**Ayrılmış örnekler (RI)** | Bu özellik, Azure 'da [ayrılmış örneklere](https://azure.microsoft.com/pricing/reserved-vm-instances/) sahip olup olmadığını belirtmenize yardımcı olur, değerlendirmede maliyet tahminleri daha sonra RI indirimlerine göre yapılır. Ayrılmış örnekler şu anda yalnızca Azure geçişi 'nde Kullandıkça Öde teklifi için desteklenmektedir.
**Boyutlandırma ölçütü** | Azure için VM 'Leri doğru şekilde boyutlandırma için kullanılacak ölçüt. Performans geçmişini dikkate almadan, *performans tabanlı* boyutlandırma yapabilir veya VM 'leri *Şirket içinde olacak şekilde boyut olarak*ayarlayabilirsiniz.
**Performans geçmişi** | Makinelerin performans verilerini değerlendirmek için göz önünde bulundurulması gereken süre. Bu özellik yalnızca boyutlandırma ölçütü *performans tabanlı*olduğunda geçerlidir.
**Yüzdebirlik kullanımı** | Doğru boyutlandırma için dikkate alınacak performans örnek kümesinin yüzdebirlik değeri. Bu özellik yalnızca boyutlandırma *performans tabanlı*olduğunda geçerlidir.
**VM serisi** |     Uygun boyutlandırma için değerlendirmek istediğiniz bir VM serisini belirtebilirsiniz. Örneğin, Azure 'da bir serisi VM 'lere geçirmeyi planlamadığınız bir üretim ortamınız varsa, bir serisi listeden veya seriden hariç tutabilir ve sağ boyutlandırma yalnızca seçili seriler içinde yapılır.
**Konfor katsayısı** | Azure geçişi sunucu değerlendirmesi, değerlendirme sırasında bir arabellek (rahatetken) kabul eder. Bu tampon, VM’lerin makine kullanım verilerinin (CPU, bellek, disk ve ağ) üzerine uygulanır. Konfor katsayısı; sezona özgü kullanım, kısa performans geçmişi ve gelecek kullanımlarda oluşabilecek artışlar gibi konuları hesaba katar.<br/><br/> Örneğin, %20 kullanıma sahip 10 çekirdekli bir VM normalde 2 çekirdekli VM ile sonuçlanır. Ancak, 2.0x konfor katsayısı ile sonuç 4 çekirdekli VM olur.
**Teklif** | Kaydolduğunuz [Azure teklifi](https://azure.microsoft.com/support/legal/offer-details/). Azure Geçişi, buna göre bir maliyet tahmini oluşturur.
**Para Birimi** | Fatura para birimi.
**İndirim (%)** | Azure teklifinin yanı sıra aldığınız, aboneliğe özgü indirim.<br/> Varsayılan ayar, %0’dır.
**VM çalışma süresi** | VM 'leriniz Azure 'da 7/24 çalışmaya devam etmeyeceklerse, çalıştırılacağı süreyi (aylık gün sayısı ve gün başına saat sayısı), maliyet tahminleri de uygun şekilde yapılacak şekilde belirtebilirsiniz.<br/> Varsayılan değer ayda 31 gün ve günde 24 saat olur.
**Azure Hibrit Avantajı** | Yazılım Güvencesi olup olmadığını ve [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-use-benefit/)için uygun olup olmadığını belirtin. Evet olarak ayarlanırsa, Windows sanal makineleri için Windows olmayan Azure fiyatları dikkate alınır. Varsayılan değer Evet’tir.


## <a name="edit-assessment-properties"></a>Değerlendirme özelliklerini Düzenle

Değerlendirme oluşturduktan sonra değerlendirme özelliklerini düzenlemek için aşağıdakileri yapın:

1. Azure geçişi projesinde **sunucular**' a tıklayın.
2. Azure **geçişi 'nde: Sunucu değerlendirmesi**, değerlendirme sayısına tıklayın.
3. **Değerlendirme**bölümünde ilgili değerlendirmeye > **Özellikleri Düzenle**' ye tıklayın.
5. Değerlendirme özelliklerini yukarıdaki tabloya göre özelleştirin.
6. Değerlendirmeyi güncelleştirmek için **Kaydet** ' e tıklayın.


Değerlendirme oluştururken değerlendirme özelliklerini de düzenleyebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Değerlendirmelerin nasıl hesaplandığı hakkında [daha fazla bilgi](concepts-assessment-calculation.md) edinin.
