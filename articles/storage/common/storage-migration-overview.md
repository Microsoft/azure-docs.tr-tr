---
title: Azure depolama geçiş kılavuzu
description: Depolama geçişine genel bakış Kılavuzu, depolama geçişi için temel Kılavuzu açıklar
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: f6f00075c7c66679281d776f9472ec4a1a590d76
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231653"
---
# <a name="azure-storage-migration-overview"></a>Azure depolama geçişine genel bakış

Bu makale, Azure 'a depolama geçişlerine odaklanmaktadır ve aşağıdaki depolama geçişi senaryolarında rehberlik sağlar:

- Dosyalar ve nesneler gibi yapılandırılmamış verilerin geçirilmesi
- Diskler ve depolama alanı ağları (San 'Lar) gibi blok tabanlı cihazların geçirilmesi

## <a name="migration-of-unstructured-data"></a>Yapılandırılmamış verileri geçirme

Yapılandırılmamış verilerin geçirilmesi aşağıdaki senaryolara sahiptir:

- Ağ bağlantılı depolamadan (NAS) Azure dosya tekliflerinden birine dosya geçişi:
  - [Azure Dosyaları](https://azure.microsoft.com/services/storage/files/)
  - [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)
  - [bağımsız yazılım satıcısı (ISV) çözümleri](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview).
- Nesne depolama çözümlerinden Azure nesne depolama platformuna nesne geçişi:
  - [Azure Blob Depolama](https://azure.microsoft.com/services/storage/blobs/)
  - [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

### <a name="migration-phases"></a>Geçiş aşamaları

Tam geçiş birkaç farklı aşamadan oluşur: bulma, değerlendirme ve geçiş.

![Geçişin keşfi, değerlendirmesi ve geçiş aşamalarını gösteren diyagram](./media/storage-migration-overview/migration-phases.png)

#### <a name="discovery-phase"></a>Bulma aşaması

Bulma aşamasında, SMB paylaşımları, NFS dışarı aktarmaları veya nesne ad alanları gibi geçirilmesi gereken tüm kaynakları belirlersiniz. Bu aşamayı el ile yapabilir veya otomatikleştirilmiş araçları kullanabilirsiniz.

#### <a name="assessment-phase"></a>Değerlendirme aşaması

Değerlendirme aşaması, geçiş için kullanılabilen seçenekleri anlamak açısından önemlidir. Geçiş sırasında riski azaltmak ve genel sınırları önlemek için şu üç adımı izleyin:

| Değerlendirme aşaması adımları                     | Seçenekler                                                                          |
|--------------------------------------------|----------------------------------------------------------------------------------|
| **Hedef depolama hizmeti seçin**            | -Azure Blob depolama ve Data Lake Storage<br>-Azure dosyaları<br>-Azure NetApp Files<br>-ISV çözümleri |
| **Bir geçiş yöntemi seçin**                  | -Çevrimiçi<br>-Çevrimdışı<br> -Her ikisinin birleşimi                                  |
| **İş için en iyi geçiş aracını seçin** | -Ticari araçlar (Azure ve ISV)<br> -Açık kaynak                             


Değerlendirme aşamasında yardımcı olabilecek birçok ticari (ISV) araç vardır. [Karşılaştırma matrisine](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)bakın.

##### <a name="choose-a-target-storage-service"></a>Hedef depolama hizmeti seçin

Hedef depolama hizmeti seçilmesi, verilere erişen uygulamaya veya kullanıcılara bağlıdır. Doğru seçim, teknik ve finansal yönlere bağlıdır. İlk olarak, olası hedefleri değerlendirmek ve gereksinimleri karşılayan hizmetleri belirlemek için teknik bir değerlendirme yapın. Daha sonra, en iyi seçimi öğrenmek için bir mali değerlendirme yapın.

Geçiş için hedef depolama hizmetini seçmenize yardımcı olmak üzere her bir hizmetin aşağıdaki yönlerini değerlendirin:

- Protokol desteği
- Performans özellikleri
- Hedef depolama hizmeti sınırları

Aşağıdaki diyagramda, önerilen Azure dosya hizmetinde size rehberlik sağlayan basitleştirilmiş bir karar ağacı bulunur. Yerel Azure hizmetleri gereksinimleri karşılamadığı takdirde, çeşitli [bağımsız yazılım satıcısı (ISV) çözümleri](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview) de olur.

Teknik değerlendirmeyi tamamladıktan sonra uygun hedefi seçtikten sonra, en düşük maliyetli seçeneği belirlemek için bir maliyet değerlendirmesi yapın.

![Doğru dosya hizmetini seçerken temel karar ağacı](./media/storage-migration-overview/files-decision-tree.png)

Karar ağacını basit tutmak için, hedef depolama hizmeti sınırları diyagramda yoktur. Geçerli sınırlar hakkında daha fazla bilgi edinmek ve bunlara göre seçimlerinizi değiştirmeniz gerekip gerekmediğini öğrenmek için bkz.:

- [Depolama hesabı sınırları](/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)
- [BLOB depolama sınırları](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-blob-storage-limits)
- [Azure Dosyaları ölçeklenebilirlik ve performans hedefleri](/azure/storage/files/storage-files-scale-targets)
- [Azure NetApp Files kaynak sınırları](/azure/azure-netapp-files/azure-netapp-files-resource-limits)

Limitlerden herhangi biri bir hizmetin kullanımıyla ilgili bir engelleyici oluşturayorsa Azure, çözümlerini Azure Marketi 'nde sunan birkaç depolama satıcısı destekler. Dosya hizmetleri sağlayan, doğrulanan ISV iş ortakları hakkında daha fazla bilgi için bkz. [birincil ve ikincil depolama Için Azure depolama iş ortakları](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview).

##### <a name="select-the-migration-method"></a>Geçiş yöntemini seçin

Depolama geçişleri için iki temel geçiş yöntemi vardır.

- **Çevrimiçi**. Çevrimiçi yöntem, veri geçişi için ağı kullanır. Genel internet ya da [Azure ExpressRoute](/azure/expressroute/expressroute-introduction) kullanılabilir. Hizmetin genel bir uç noktası yoksa, genel İnternet ile bir VPN kullanmanız gerekir.
- **Çevrimdışı.** Çevrimdışı Yöntem [Azure Data Box](https://azure.microsoft.com/services/databox/) cihazlarından birini kullanır.

Çevrimiçi bir yöntemi ve çevrimdışı bir yöntemi kullanma kararı, kullanılabilir ağ bant genişliğine bağlıdır. Çevrimiçi yöntem, gerekli zaman çizelgesinde geçiş yapmak için yeterli ağ bant genişliğinin olduğu durumlarda tercih edilir.

İlk toplu geçiş için her iki yöntemin bir birleşimini ve değişikliklerin artımlı geçişi için çevrimiçi bir yöntemi kullanmak mümkündür. Her iki yöntemin de aynı anda kullanılması yüksek düzeyde koordinasyon gerektirir ve bu nedenle önerilmez. Her iki yöntemi de kullanmayı seçerseniz, çevrimdışı geçirilen veri kümelerinden çevrimiçi olarak geçirilen veri kümelerini yalıtabilirsiniz.

Farklı geçiş yöntemleri ve yönergeleri hakkında daha fazla bilgi için bkz. [veri aktarımı Için Azure çözümü seçme](/azure/storage/common/storage-choose-data-transfer-solution) ve [Azure dosya paylaşımlarına geçiş](/azure/storage/files/storage-files-migration-overview).

##### <a name="choose-the-best-migration-tool-for-the-job"></a>İş için en iyi geçiş aracını seçin

Geçişi gerçekleştirmek için kullanabileceğiniz çeşitli geçiş araçları vardır. Bazıları, AzCopy, Robocopy, xcopy ve rsync gibi bazı açık kaynaktır, diğerleri ise ticari bir şekilde yapılır. Kullanılabilir ticari araçların listesi ve bunlar arasındaki karşılaştırma, [karşılaştırma matremizdeki](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)kullanılabilir.

Açık kaynaklı araçlar, küçük ölçekli geçişler için idealdir. Windows dosya sunucularından Azure dosyalarına geçiş için, Microsoft Azure dosyaları yerel özelliği ve [Azure dosya eşitleme](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-file-sync)kullanarak başlamasını önerir. Farklı kaynaklardan, büyük kapasiteye veya denetim özelliklerine sahip azaltma veya ayrıntılı raporlama gibi özel gereksinimlerden oluşan daha karmaşık geçişler için, ticari araçlar en iyi seçenektir. Bu araçlar, geçişi daha kolay hale getirir ve riski önemli ölçüde azaltır. Birçok ticari araç, değerlendirme için değerli bir giriş sağlayan bulma işlemini de gerçekleştirebilir.

#### <a name="migration-phase"></a>Geçiş aşaması

Geçiş aşaması, veri taşıma ve geçiş işlemi gerçekleştiren son geçiş adımıdır. Genellikle, daha kolay bir şekilde geçiş yapmak için geçiş aşamasından birkaç kez ilerleyin. Geçiş aşaması aşağıdaki adımlardan oluşur:

1. **İlk geçiş.** İlk geçiş adımı kaynaktaki tüm verileri hedefe geçirir. Bu adım, geçirilmesi gereken verilerin toplu kısmını geçirir.
2. **Dan.** Yeniden eşitleme işlemi, ilk geçiş adımından sonra değiştirilen tüm verileri geçirir. Çok sayıda değişiklik varsa, bu adımı birkaç kez tekrarlayabilirsiniz. Birden çok yeniden eşitleme işlemini çalıştırmanın hedefi, son adım için geçen süreyi azaltmaktır. Etkin olmayan veriler ve değişiklik olmayan veriler (yedekleme veya arşiv verileri gibi) için bu adımı atlayabilirsiniz.
3. **Son geçiş**. Son geçiş adımı, verilerin etkin kullanımını kaynaktan hedefe geçirir ve kaynağı yeniden oluşturur.

Yapılandırılmamış veriler için geçişin süresi birkaç yönüyle farklılık gösterir. Seçilen yöntemin dışında, en kritik etmenler verilerin ve dosya boyutu dağıtımının toplam boyutudur. Toplam veri kümesi arttıkça geçiş süresi de artar. Ortalama dosya boyutu ne kadar küçükse geçiş süresi de o kadar uzun olur. Çok sayıda küçük dosyanız varsa, toplam geçiş süresini azaltmak için, varsa bunları daha büyük dosyalarda (bir. tar veya. zip dosyası gibi) arşivlemeyi düşünün.

## <a name="migration-of-block-based-devices"></a>Blok tabanlı cihazların geçirilmesi

Blok tabanlı cihazların geçirilmesi, genellikle sanal makinenin veya fiziksel konak geçişinin bir parçası olarak yapılır. Geçiş işleminden sonra blok depolama kararlarını geciktirmede yaygın bir yanıltıcı olur. İş yükü gereksinimleri için uygun hususlar sayesinde bu kararların zaman önüne alınması, buluta daha yumuşak bir geçişe neden olur.

Geçirilecek iş yüklerini ve yapılacak yaklaşımı araştırmak için, [disk depolama ürün sayfasındaki](https://azure.microsoft.com/services/storage/disks/#resources) [Azure disk depolama belgelerine](/azure/virtual-machines/disks-types)ve kaynaklarına bakın. Gereksinimlerinize uyan diskler ve [disk](/azure/virtual-machines/disk-bursting)patlaması gibi en son yetenekler hakkında bilgi edinebilirsiniz. Sanal makineleri temeldeki blok tabanlı cihazlarla birlikte geçirme hakkında daha fazla bilgi için bkz. [Azure geçiş](/azure/migrate/) belgeleri.

## <a name="see-also"></a>Ayrıca bkz.

- [Veri aktarımı için bir Azure çözümü seçme](/azure/storage/common/storage-choose-data-transfer-solution)
- [Ticari geçiş araçları karşılaştırması](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)
- [Azure dosya paylaşımlarına geçirme](/azure/storage/files/storage-files-migration-overview)
- [Azure için WANdisco LiveData platform ile Data Lake Storage geçirin](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [AzCopy ile verileri Azure depolama 'ya kopyalama veya taşıma](https://aka.ms/azcopy)
- [Büyük veri kümelerini AzReplicate ile Azure Blob depolamaya geçirme](https://github.com/Azure/AzReplicate/tree/master/)