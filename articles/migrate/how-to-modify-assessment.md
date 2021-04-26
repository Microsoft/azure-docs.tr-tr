---
title: Azure geçişi için değerlendirmeleri özelleştirin | Microsoft Docs
description: Azure geçişi ile oluşturulan değerlendirmelerin nasıl özelleştirileceğini açıklar.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: 9bda4750f6b4340399bbbe070954dd23930b1ae1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785215"
---
# <a name="customize-an-assessment"></a>Bir değerlendirmeyi özelleştirme

Bu makalede, Azure geçişi bulma ve değerlendirme aracı tarafından oluşturulan değerlendirmelerin nasıl özelleştirileceği açıklanır.

[Azure geçişi](migrate-services-overview.md) , şirket içi uygulamalarınızı, iş yüklerinizi ve özel/genel bulut VM 'lerini Azure 'a bulmayı, değerlendirmeyi ve geçirmeyi izlemek için bir merkezi Merkez sağlar. Hub, değerlendirme ve geçiş için Azure geçiş araçları ve ayrıca üçüncü taraf bağımsız yazılım satıcısı (ISV) teklifleri sağlar.

Azure 'a geçiş için hazırlık bölümünde şirket içi VMware VM 'leri ve Hyper-V VM 'Leri için değerlendirmeler oluşturmak üzere Azure geçişi bulma ve değerlendirme aracını kullanabilirsiniz. Bulma ve değerlendirme aracı, Azure IaaS sanal makinelerine ve Azure VMware çözümüne (AVS) geçiş için şirket içi sunucular değerlendirir. 

## <a name="about-assessments"></a>Değerlendirmeler hakkında

Bulma ve değerlendirme aracı ile oluşturduğunuz değerlendirmeler, verilerin bir zaman noktası anlık görüntüsüdür. Azure geçişi: bulma ve değerlendirme kullanarak oluşturabileceğiniz iki tür değerlendirme vardır.

**Değerlendirme Türü** | **Ayrıntılar**
--- | --- 
**Azure VM** | Şirket içi sunucularınızı Azure sanal makinelerine geçirmeye yönelik değerlendirmeler. <br/><br/> Bu değerlendirme türünü kullanarak Azure 'a geçiş için şirket içi [VMware VM](how-to-set-up-appliance-vmware.md)'lerinizi, [Hyper-V sanal](how-to-set-up-appliance-hyper-v.md)makinelerinizi ve [fiziksel sunucuları](how-to-set-up-appliance-physical.md) değerlendirebilirsiniz. (concepts-assessment-calculation.md)
**Azure VMware Çözümü (AVS)** | Şirket içi sunucularınızı [Azure VMware Çözümü'ne (AVS)](../azure-vmware/introduction.md) geçirmeye yönelik değerlendirmeler. <br/><br/> Bu değerlendirme türünü kullanarak şirket içi ortamınızdaki [VMware VM'lerinizi](how-to-set-up-appliance-vmware.md) Azure VMware Çözümü (AVS) geçişi için değerlendirebilirsiniz. [Daha fazla bilgi edinin](concepts-azure-vmware-solution-assessment-calculation.md)

Azure geçiş değerlendirmelerinde boyutlandırma ölçütü seçenekleri:

**Boyutlandırma ölçütü** | **Ayrıntılar** | **Veriler**
--- | --- | ---
**Performans tabanlı** | Toplanan performans verilerine göre öneriler sunan değerlendirmeler | **Azure VM değerlendirmesi**: VM boyutu önerisi, CPU ve bellek kullanımı verilerini dikkate alır.<br/><br/> Disk türü önerisi (standart HDD/SSD veya premium yönetilen diskler), şirket içi ortamda bulunan disklerin IOPS ve aktarım hızı verilerini dikkate alır.<br/><br/>**Azure SQL değerlendirmesi**: Azure SQL YAPıLANDıRMASı; CPU kullanımı, bellek kullanımı, IOPS (veri ve günlük dosyaları), aktarım hızı ve GÇ işlemlerinin gecikme SÜRESINI içeren SQL örnekleri ve veritabanlarının performans verilerini temel alır.<br/><br/>**Azure VMware Çözümü (AVS) değerlendirmesi**: AVS düğümü önerisi, CPU ve bellek kullanımı verilerini dikkate alır.
**Şirket içinde olduğu gibi** | Bu değerlendirmeler, öneride bulunmak için performans verilerini kullanmaz. | **Azure VM değerlendirmesi**: VM boyutu önerisi, şirket içi VM boyutunu temel alır<br/><br> Önerilen disk türü, değerlendirme için belirlediğiniz depolama türü ayarına bağlıdır.<br/><br/> **Azure VMware Çözümü (AVS) değerlendirmesi**: AVS düğümü önerisi, şirket içi VM boyutunu temel alır.

## <a name="how-is-an-assessment-done"></a>Değerlendirme nasıl yapılır?

Azure geçişi bulma ve değerlendirmede gerçekleştirilen bir değerlendirme üç aşamaya sahiptir. Değerlendirme, bir uygunluk analizi, ardından boyutlandırma ve son olarak aylık maliyet tahmini ile başlar. Bir makine, bir öncekini geçerse daha sonraki bir aşamaya geçer. Örneğin, bir makine Azure uygunluk denetiminde başarısız olursa, Azure için uygun değil olarak işaretlenir ve boyutlandırma ve maliyetlendirme yapılmaz. [Daha fazla bilgi edinin.](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>Azure VM değerlendirmesinde neler var?

**Özellik** | **Ayrıntılar**
--- | ---
**Hedef konum** | Geçişi yapmak istediğiniz Azure konumu.<br/> Azure VM değerlendirmesi Şu anda şu hedef bölgeleri destekliyor: Avustralya Doğu, Avustralya Güneydoğu, Brezilya Güney, Kanada Orta, Kanada Doğu, Orta Hindistan, Orta ABD, Çin Doğu, Çin Kuzey, Doğu Asya, Doğu ABD, Doğu ABD2, Almanya Orta, Almanya kuzeydoğu, Japonya Doğu, Japonya Batı, Kore Orta, Kore Güney, Orta Kuzey ABD, Kuzey Avrupa, Orta Güney ABD, Güneydoğu Asya, Güney Hindistan, UK Güney, UK Batı, US Gov Arizona , US Gov Teksas, US Gov Virginia, Orta Batı ABD, Batı Avrupa, Batı Hindistan, Batı ABD ve Batı ABD2.
**Depolama türü** | Azure 'da, ' ye taşımak istediğiniz disk türünü belirtmek için bu özelliği kullanabilirsiniz.<br/><br/> Şirket içi boyutlandırma için, hedef depolama türünü Premium yönetilen diskler, Standart SSD yönetilen diskler veya Standart HDD tarafından yönetilen diskler olarak belirtebilirsiniz. Performans tabanlı boyutlandırma için, hedef disk türünü otomatik, Premium yönetilen diskler, Standart HDD yönetilen diskler veya Standart SSD tarafından yönetilen diskler olarak belirtebilirsiniz.<br/><br/> Depolama türünü otomatik olarak belirttiğinizde, disk önerisi, disklerin performans verilerine (ıOPS ve aktarım hızı) göre yapılır. Depolama türünü Premium/standart olarak belirtirseniz, değerlendirme, seçilen depolama türü içinde bir disk SKU 'SU önerir. % 99,9 tek örnekli bir VM SLA 'Sı elde etmek istiyorsanız, depolama türünü Premium ile yönetilen diskler olarak belirtmek isteyebilirsiniz. Bu, değerlendirmede tüm disklerin Premium tarafından yönetilen diskler olarak önerilmesini sağlar. Azure
**Ayrılmış örnekler (RI)** | Bu özellik, Azure 'da [ayrılmış örneklere](https://azure.microsoft.com/pricing/reserved-vm-instances/) sahip olup olmadığını belirtmenize yardımcı olur, değerlendirmede maliyet tahminleri daha sonra RI indirimlerine göre yapılır. Ayrılmış örnekler şu anda yalnızca Azure geçişi 'nde Kullandıkça Öde teklifi için desteklenmektedir.
**Boyutlandırma ölçütü** | Azure için VM 'Leri doğru şekilde boyutlandırma için kullanılacak ölçüt. Performans geçmişini dikkate almadan, *performans tabanlı* boyutlandırma yapabilir veya VM 'leri *Şirket içinde olacak şekilde boyut olarak* ayarlayabilirsiniz.
**Performans geçmişi** | Makinelerin performans verilerini değerlendirmek için göz önünde bulundurulması gereken süre. Bu özellik yalnızca boyutlandırma ölçütü *performans tabanlı* olduğunda geçerlidir.
**Yüzdebirlik kullanımı** | Doğru boyutlandırma için dikkate alınacak performans örnek kümesinin yüzdebirlik değeri. Bu özellik yalnızca boyutlandırma *performans tabanlı* olduğunda geçerlidir.
**VM serisi** |     Uygun boyutlandırma için değerlendirmek istediğiniz bir VM serisini belirtebilirsiniz. Örneğin, Azure 'da bir serisi VM 'lere geçirmeyi planlamadığınız bir üretim ortamınız varsa, bir serisi listeden veya seriden hariç tutabilir ve sağ boyutlandırma yalnızca seçili seriler içinde yapılır.
**Konfor katsayısı** | Azure VM değerlendirmesi, değerlendirme sırasında bir arabellek (rahatetken) kabul eder. Bu tampon, VM’lerin makine kullanım verilerinin (CPU, bellek, disk ve ağ) üzerine uygulanır. Konfor katsayısı; sezona özgü kullanım, kısa performans geçmişi ve gelecek kullanımlarda oluşabilecek artışlar gibi konuları hesaba katar.<br/><br/> Örneğin, %20 kullanıma sahip 10 çekirdekli bir VM normalde 2 çekirdekli VM ile sonuçlanır. Ancak, 2.0x konfor katsayısı ile sonuç 4 çekirdekli VM olur.
**Teklif** | Kaydolduğunuz [Azure teklifi](https://azure.microsoft.com/support/legal/offer-details/). Azure Geçişi, buna göre bir maliyet tahmini oluşturur.
**Para Birimi** | Fatura para birimi.
**İndirim (%)** | Azure teklifinin yanı sıra aldığınız, aboneliğe özgü indirim.<br/> Varsayılan ayar, %0’dır.
**VM çalışma süresi** | VM 'leriniz Azure 'da 7/24 çalışmaya devam etmeyeceklerse, çalıştırılacağı süreyi (aylık gün sayısı ve gün başına saat sayısı), maliyet tahminleri de uygun şekilde yapılacak şekilde belirtebilirsiniz.<br/> Varsayılan değer ayda 31 gün ve günde 24 saat olur.
**Azure Hibrit Avantajı** | Yazılım Güvencesi olup olmadığını ve [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-use-benefit/)için uygun olup olmadığını belirtin. Evet olarak ayarlanırsa, Windows sanal makineleri için Windows olmayan Azure fiyatları dikkate alınır. Varsayılan değer Evet’tir.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Azure VMware Çözüm (AVS) değerlendirmesi nelerdir?

Aşağıda bir AVS değerlendirmesi verilmiştir:


| **Özellik** | **Ayrıntılar** |
| - | - |
| **Hedef konum** | Geçirmek istediğiniz AVS özel bulut konumunu belirtir.<br/><br/> AVS değerlendirmesi Şu anda şu hedef bölgeleri destekliyor: Doğu ABD, Batı Avrupa Batı ABD. |
| **Depolama türü** | AVS 'de kullanılacak depolama altyapısını belirtir.<br/><br/> AVS değerlendirmelerinin yalnızca varsayılan depolama türü olarak vSAN 'ı desteklediğini unutmayın. |
**Ayrılmış örnekler (RIS)** | Bu özellik, AVS 'de ayrılmış örnekler belirtmenize yardımcı olur. Bu, AVS düğümlerinde Şu anda desteklenmemektedir. |
**Düğüm türü** | Şirket içi VM 'Leri eşlemek için kullanılan [AVS düğüm türünü](../azure-vmware/concepts-private-clouds-clusters.md) belirtir. Varsayılan düğüm türünün AV36 olduğunu unutmayın. <br/><br/> Azure geçişi, sanal makinelerin AVS 'ye geçirilmesi için gerekli sayıda düğüm önermenizi önerir. |
**FTT ayarı, RAID düzeyi** | Tolerans ve RAID birleşimlerine yönelik geçerli hatayı belirtir. Şirket içi VM disk gereksinimiyle birlikte bulunan seçili FTT seçeneği, AVS 'de gereken toplam vSAN depolama alanını tespit eder. |
**Boyutlandırma ölçütü** | AVS için sanal makineleri _doğru olarak boyutlandıralmak_ üzere kullanılacak ölçütleri ayarlar. Performans geçmişini dikkate almadan _performans tabanlı_ boyutlandırmayı veya _Şirket içi olarak_ tercih edebilirsiniz. |
**Performans geçmişi** | Makinelerin performans verilerini değerlendirmek için göz önünde bulundurulması gereken süreyi ayarlar. Bu özellik yalnızca boyutlandırma ölçütü _performans tabanlı_ olduğunda geçerlidir. |
**Yüzdebirlik kullanımı** | Sağ boyutlandırmanın kabul edileceği performans örneği kümesinin yüzdebirlik değerini belirtir. Bu özellik yalnızca boyutlandırma performans tabanlı olduğunda geçerlidir.|
**Konfor katsayısı** | Azure Geçişi, değerlendirme sırasında bir tamponu (konfor katsayısı) göz önünde bulundurur. Bu tampon, VM’lerin makine kullanım verilerinin (CPU, bellek, disk ve ağ) üzerine uygulanır. Konfor katsayısı; sezona özgü kullanım, kısa performans geçmişi ve gelecek kullanımlarda oluşabilecek artışlar gibi konuları hesaba katar.<br/><br/> Örneğin, %20 kullanıma sahip 10 çekirdekli bir VM normalde 2 çekirdekli VM ile sonuçlanır. Ancak, 2.0x konfor katsayısı ile sonuç 4 çekirdekli VM olur. |
**Teklif** | Kayıtlı olduğunuz [Azure teklifini](https://azure.microsoft.com/support/legal/offer-details/) görüntüler. Azure Geçişi, buna göre bir maliyet tahmini oluşturur.|
**Para Birimi** | Hesabınız için faturalandırma para birimini gösterir. |
**İndirim (%)** | Azure teklifinin üzerine aldığınız aboneliğe özgü tüm indirimi listeler. Varsayılan ayar, %0’dır. |
**Azure Hibrit Avantajı** | Yazılım Güvencesi olup olmadığını ve [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-use-benefit/)uygun olduğunu belirtir. Düğüm tabanlı fiyat nedeniyle Azure VMware Çözüm fiyatlandırması üzerinde hiçbir etkisi olmamasına karşın, müşteriler Azure hibrit avantajlarını kullanarak AVS 'ye şirket içi işletim sistemi lisanslarını (Microsoft tabanlı) uygulayabilirler. Diğer yazılım işletim sistemi satıcılarının, RHEL gibi kendi lisans şartlarını sağlaması gerekecektir. |
**vCPU fazla abonelik** | AVS düğümündeki 1 fiziksel çekirdeğe bağlı sanal çekirdek sayısının oranını belirtir. Hesaplamalardaki varsayılan değer 4 vCPU: AVS 'de 1 fiziksel çekirdek olur. <br/><br/> API kullanıcıları bu değeri bir tamsayı olarak ayarlayabilir. VCPU fazla aboneliği > 4:1, performans düşüşüne neden olabilir ancak Web sunucusu türü iş yükleri için kullanılabilir. |

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Azure SQL değerlendirmesi oluşturmak ve özelleştirmek için hangi Özellikler kullanılır?

Azure SQL değerlendirmesi özelliklerine aşağıda verilmiştir:

**Özellik** | **Ayrıntılar**
--- | ---
**Hedef konum** | Geçirmek istediğiniz Azure bölgesi. Azure SQL yapılandırması ve maliyet önerileri belirttiğiniz konuma göre yapılır.
**Hedef dağıtım türü** | Değerlendirmeyi çalıştırmak istediğiniz hedef dağıtım türü: <br/><br/> Azure geçişi 'nin Azure SQL MI ve Azure SQL DB 'ye geçiş için SQL sunucularınızın hazır olduğunu değerlendirmesini ve en uygun hedef dağıtım seçeneğini, hedef katmanını, Azure SQL yapılandırmasını ve aylık tahminleri önermesini istiyorsanız, **Önerilen**' i seçin.<br/><br/>SQL Server 'larınızı yalnızca Azure SQL veritabanlarına geçirmeye değerlendirmek ve hedef katmanı, Azure SQL DB yapılandırması ve aylık tahminleri gözden geçirmek istiyorsanız **Azure SQL veritabanı**' nı seçin.<br/><br/>SQL Server 'larınızı yalnızca Azure SQL veritabanlarına geçirmek için değerlendirmek istiyorsanız Azure SQL **mi**' yi seçin ve hedef katmanını, Azure SQL mı yapılandırmasını ve aylık tahminleri gözden geçirin.
**Ayrılmış kapasite** | Değerlendirmede maliyet tahminleri bunları hesaba alacak şekilde ayrılmış kapasiteyi belirtir.<br/><br/> Ayrılmış kapasite seçeneğini belirlerseniz, "Indirim (%)" belirtemezsiniz.
**Boyutlandırma ölçütü** | Bu özellik, Azure SQL yapılandırmasını sağ boyuta almak için kullanılır. <br/><br/> Varsayılan olarak, **performans tabanlı** olarak ayarlanır; Bu, değerlendirmenin en iyi şekilde boyutlandırılmış BIR Azure SQL yönetilen örneği ve/veya Azure SQL veritabanı katmanı/yapılandırma önerisi önermek için SQL Server örnekleri ve veritabanlarının performans ölçümlerini toplayacağı anlamına gelir.
**Performans geçmişi** | Performans geçmişi performans verileri değerlendirilirken kullanılan süreyi belirtir.
**Yüzdebirlik kullanımı** | Yüzdebirlik kullanımı, doğru hale getirmek için kullanılan performans örneğinin yüzdebirlik değerini belirtir.
**Konfor katsayısı** | Değerlendirme sırasında kullanılan arabellek. Dönemsel kullanım, kısa performans geçmişi ve gelecekteki kullanımlarda olası artışlar gibi sorunlar için BT hesapları.<br/><br/> Örneğin, %20 kullanımı olan 10 çekirdekli bir örnek, normalde iki çekirdekli örnekle sonuçlanır. 2,0 ile, sonuç olarak dört çekirdekli bir örnek olur.
**Teklif/lisanslama programı** | Kayıtlı olduğunuz [Azure teklifi](https://azure.microsoft.com/support/legal/offer-details/) . Şu anda yalnızca Kullandıkça öde ve kullandıkça öde geliştirme/test seçeneklerinden birini belirleyebilirsiniz. Ayrılmış kapasiteyi uygulayarak ve kullandıkça öde teklifinin üzerine Azure Hibrit Avantajı ek indirim sunabileceğinize dikkat edin.
**Hizmet katmanı** | Azure SQL veritabanı ve/veya Azure SQL yönetilen örneği 'ne geçiş için iş ihtiyaçlarınızı karşılamak üzere en uygun hizmet katmanı seçeneği:<br/><br/>Azure geçişi 'nin sunucularınız için en uygun hizmet katmanını önermesini istiyorsanız **önerilir** . Bu genel amaçlı veya Iş açısından kritik olabilir. <br/><br/> **Genel amaçlı** Bütçe odaklı iş yükleri için tasarlanan bir Azure SQL yapılandırması istiyorsanız. [Daha Fazla Bilgi](https://docs.microsoft.com/azure/azure-sql/database/service-tier-general-purpose) <br/><br/> **İş açısından kritik** Düşük gecikmeli iş yükleri için tasarlanan bir Azure SQL yapılandırmasının, hatalara ve hızlı yük devretmeler ile yüksek dayanıklılık sağlamak istiyorsanız. [Daha Fazla Bilgi](https://docs.microsoft.com/azure/azure-sql/database/service-tier-business-critical)
**Para Birimi** | Hesabınız için faturalandırma para birimi.
**İndirim (%)** | Azure teklifinin üzerine aldığınız, aboneliğe özgü tüm indirimler. Varsayılan ayar, %0’dır.
**Azure Hibrit Avantajı** | Zaten bir SQL Server lisansınızın olup olmadığını belirtir. <br/><br/> Bunu yaptıysanız ve SQL Server aboneliklerinin etkin yazılım güvencesi kapsamında ele alındıklarında, lisansları Azure 'a getirdiğinizde Azure Hibrit Avantajı uygulayabilirsiniz.

## <a name="edit-assessment-properties"></a>Değerlendirme özelliklerini Düzenle

Değerlendirme oluşturduktan sonra değerlendirme özelliklerini düzenlemek için aşağıdakileri yapın:

1. Azure geçişi projesinde **sunucular**' a tıklayın.
2. **Azure geçişi: bulma ve değerlendirme** bölümünde, değerlendirme sayısı ' na tıklayın.
3. **Değerlendirme** bölümünde ilgili değerlendirmeye > **Özellikleri Düzenle**' ye tıklayın.
5. Yukarıdaki tablolara uygun olarak değerlendirme özelliklerini özelleştirin.
6. Değerlendirmeyi güncelleştirmek için **Kaydet** ' e tıklayın.


Değerlendirme oluştururken değerlendirme özelliklerini de düzenleyebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

- Azure VM değerlendirmelerinin nasıl hesaplandığı hakkında [daha fazla bilgi edinin](concepts-assessment-calculation.md) .
- Azure SQL değerlendirmelerinin nasıl hesaplandığı hakkında [daha fazla bilgi edinin](concepts-azure-sql-assessment-calculation.md) .
- AVS değerlendirmelerinin nasıl hesaplandığı hakkında [daha fazla bilgi edinin](concepts-azure-vmware-solution-assessment-calculation.md) .

