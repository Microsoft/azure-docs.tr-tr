---
title: Veri korumaya genel bakış
titleSuffix: Azure Storage
description: BLOB depolama ve Azure Data Lake Storage 2. verileriniz için kullanılabilen veri koruma seçenekleri, verilerinizin silinmesini veya üzerine yazılmasını korumanıza olanak sağlar. Silinen veya üzerine yazılmış verileri kurtarmanız gerekiyorsa, bu kılavuz senaryonuz için en uygun kurtarma seçeneğini seçmenize yardımcı olabilir.
services: storage
author: tamram
ms.service: storage
ms.date: 04/09/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: prishet
ms.subservice: common
ms.openlocfilehash: 90c83397089b77d30694041a37debc0731ea2a38
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304265"
---
# <a name="data-protection-overview"></a>Veri korumaya genel bakış

Azure depolama, BLOB depolama ve silinen veya üzerine yazılmış verileri kurtarmanız gereken senaryolara hazırlanmanıza yardımcı olmak üzere Azure Data Lake Storage 2. için veri koruması sağlar. Bir olay gerçekleşmeden önce verilerinizi en iyi şekilde korumak için onu tehlikeye önünde bulundurmak önemlidir. Bu kılavuz, senaryonuzun hangi veri koruma özelliklerine ve bunların nasıl uygulanacağını önceden belirlemenize yardımcı olabilir. Silinen veya üzerine yazılmış verileri kurtarmanız gerekiyorsa, bu genel bakış, senaryonuza bağlı olarak nasıl devam edeceğine ilişkin yönergeler de sağlar.

Azure depolama belgelerinde, *veri koruma* , depolama hesabını ve içindeki verileri silme veya değiştirme veya silindikten sonra verileri geri yükleme stratejilerini ifade eder. Azure depolama, *olağanüstü durum kurtarma* için, donanım sorunları veya doğal olağanüstü durumlar nedeniyle verilerinizi hizmet kesintilerine karşı korumak üzere birden çok artıklık düzeyi ve birincil bölgedeki veri merkezinin kullanılamaz hale geldiği olaydaki müşteri tarafından yönetilen yük devretme da dahil olmak üzere çok sayıda yedekleme seçeneği sunar. Verilerinizin hizmet kesintilerine nasıl korunduğu hakkında daha fazla bilgi için bkz. [olağanüstü durum kurtarma](#disaster-recovery).

## <a name="recommendations-for-basic-data-protection"></a>Temel veri koruma önerileri

Depolama hesabınız için temel veri koruma kapsamına ve içerdiği verilere bakıyorsanız, Microsoft ile başlamak için aşağıdaki adımları almayı önerir:

- Hesabı silme veya yapılandırma değişikliklerinden korumak için depolama hesabında bir Azure Resource Manager kilidi yapılandırın. [Daha fazla bilgi edinin...](../common/lock-account-resource.md)
- Silinen kapsayıcıyı ve içeriğini kurtarmak üzere depolama hesabı için kapsayıcı geçici silme özelliğini etkinleştirin. [Daha fazla bilgi edinin...](soft-delete-container-enable.md)
- Bir Blobun durumunu düzenli aralıklarla kaydet:
  - BLOB depolama iş yükleri için blob sürümü oluşturma 'yı etkinleştirin ve BLOB 'un üzerine yazıldığında verilerinizin durumunu otomatik olarak kaydedin. [Daha fazla bilgi edinin...](versioning-enable.md)
  - Azure Data Lake Storage iş yükleri için, verilerinizin durumunu belirli bir zamanda kaydetmek üzere el ile anlık görüntü alın. [Daha fazla bilgi edinin...](snapshots-overview.md)

Bu seçeneklerin yanı sıra diğer senaryolara yönelik ek veri koruma seçenekleri de aşağıdaki bölümde daha ayrıntılı olarak açıklanmıştır.

Bu özelliklerle ilgili maliyetlere genel bir bakış için bkz. [Maliyet konularının Özeti](#summary-of-cost-considerations).

## <a name="overview-of-data-protection-options"></a>Veri koruma seçeneklerine genel bakış

Aşağıdaki tabloda, genel veri koruma senaryolarında Azure Storage 'da kullanılabilen seçenekler özetlenmektedir. Size sunulan seçenekler hakkında daha fazla bilgi edinmek için durumunuza uygun olan senaryoları seçin. Hiyerarşik bir ad alanı etkin olan depolama hesapları için şu anda tüm özelliklerin mevcut olmadığından emin olmanız gerektiğini unutmayın.

| Senaryo | Veri koruma seçeneği | Öneriler | Koruma avantajı | Data Lake Storage için kullanılabilir |
|--|--|--|--|--|
| Bir depolama hesabının silinmesini veya değiştirilmesini engelleyin. | Azure Resource Manager kilidi<br />[Daha fazla bilgi edinin...](../common/lock-account-resource.md) | Depolama hesabının silinmesini engellemek için tüm depolama hesaplarınızı bir Azure Resource Manager kilidi ile kilitleyin. | Depolama hesabını silme veya yapılandırma değişikliklerine karşı korur.<br /><br />, Hesaptaki kapsayıcıların veya Blobların silinmesini veya üzerine yazılmasını korumaz. | Yes |
| Bir kapsayıcının ve bloblarının, denetlediğiniz bir aralığa göre silinmesini veya değiştirilmesini engelleyin. | Bir kapsayıcıda dengeszlik kullanılabilirliği ilkesi<br />[Daha fazla bilgi edinin...](storage-blob-immutable-storage.md) | İş açısından kritik belgeleri korumak için, örneğin yasal veya yasal uyumluluk gereksinimlerini karşılamak üzere bir kapsayıcı üzerinde bir dengesyabilirlik ilkesi ayarlayın. | Bir kapsayıcıyı ve bloblarını tüm silmeler ve üzerine yazma olarak korur.<br /><br />Yasal bir saklama veya kilitli zaman tabanlı bekletme ilkesi etkin olduğunda, depolama hesabı da silinmeye karşı korunur. Hiçbir imlebilirlik kullanılabilirliği ilkesinin ayarlandığı kapsayıcılar silme işleminden korunmaz. | Evet, önizlemede |
| Silinen bir kapsayıcıyı belirtilen bir Aralık içinde geri yükleyin. | Kapsayıcı geçici silme (Önizleme)<br />[Daha fazla bilgi edinin...](soft-delete-container-overview.md) | En az 7 günlük bekletme aralığı ile tüm depolama hesapları için kapsayıcı geçici silmeyi etkinleştirin.<br /><br />Kapsayıcıda tek tek Blobları korumak için blob sürümü oluşturma ve BLOB geçici silme özelliğini kapsayıcı geçici silme özelliğiyle birlikte etkinleştirin.<br /><br />Farklı saklama dönemleri gerektiren kapsayıcıları ayrı depolama hesaplarında saklayın. | Silinen kapsayıcı ve içerikleri saklama dönemi içinde geri yüklenebilir.<br /><br />Yalnızca kapsayıcı düzeyindeki işlemler (ör. [kapsayıcıyı sil](/rest/api/storageservices/delete-container)) geri yüklenebilir. Kapsayıcı geçici silme, blob silinirse tek bir Blobun kapsayıcıyı geri yüklemenize imkan vermez. | Evet, önizlemede |
| Üzerine yazıldığında, bir Blobun durumunu önceki bir sürümde otomatik olarak kaydet. | Blob sürümü oluşturma<br />[Daha fazla bilgi edinin...](versioning-overview.md) | Blob verileri için en iyi korumaya ihtiyacınız olan depolama hesapları için kapsayıcı geçici silme ve BLOB geçici silme ile birlikte blob sürüm oluşturmayı etkinleştirin.<br /><br />Maliyetleri sınırlamak üzere ayrı bir hesapta sürüm oluşturma gerektirmeyen blob verilerini depolayın. | Her blob yazma işlemi için yeni bir sürüm oluşturulur. Geçerli sürüm silinirse veya üzerine yazılırsa, bir Blobun geçerli sürümü önceki bir sürümden geri yüklenebilir. | Hayır |
| Silinen bir Blobu veya blob sürümünü belirtilen bir Aralık içinde geri yükleyin. | Blob geçici silme<br />[Daha fazla bilgi edinin...](soft-delete-blob-overview.md) | En az 7 günlük bekletme aralığı ile tüm depolama hesapları için blob geçici silme özelliğini etkinleştirin.<br /><br />Blob verilerini en iyi şekilde koruması için blob sürüm oluşturma ve kapsayıcı geçici silme özelliğini blob geçici silme ile birlikte etkinleştirin.<br /><br />Ayrı depolama hesaplarında farklı saklama süreleri gerektiren blob 'ları depolayın. | Silinen bir blob veya blob sürümü Bekletme dönemi içinde geri yüklenebilir. | Hayır |
| Blok Blobları kümesini zaman içinde önceki bir noktaya geri yükleyin. | Belirli bir noktaya geri yükleme<br />[Daha fazla bilgi edinin...](point-in-time-restore-overview.md) | Önceki bir duruma geri dönmek üzere belirli bir noktaya geri yükleme kullanmak için, kapsayıcıyı silmek yerine tek tek blok bloblarını silmek üzere uygulamanızı tasarlayın. | Blok Blobları kümesi, geçmişteki belirli bir noktada durumlarına geri döndürülebilir.<br /><br />Yalnızca blok Blobları üzerinde gerçekleştirilen işlemler geri döndürülür. Kapsayıcılar, sayfa Blobları veya ekleme Blobları üzerinde gerçekleştirilen işlemler geri döndürülemez. | Hayır |
| Bir Blobun durumunu belirli bir zaman noktasına el ile kaydedin. | Blob anlık görüntüsü<br />[Daha fazla bilgi edinin...](snapshots-overview.md) | Sürüm oluşturma senaryolarınız için uygun olmadığında, maliyet veya diğer hususlar nedeniyle ya da depolama hesabında hiyerarşik bir ad alanı etkinken blob sürümü oluşturma için bir alternatif olarak önerilir. | Blob üzerine yazılırsa blob bir anlık görüntüden geri yüklenebilir. Blob silinirse, anlık görüntüler de silinir. | Evet, önizlemede |
| Blob silinebilir veya üzerine yazılabilir, ancak veriler düzenli olarak ikinci bir depolama hesabına kopyalanır. | Azure Storage nesne çoğaltma veya AzCopy veya Azure Data Factory gibi bir araç kullanarak verileri ikinci bir hesaba kopyalamak için kendinize yönelik bir çözümdür. | Beklenmeyen bilinçli eylemlere veya öngörülemeyen senaryolara karşı içinizin rahat korunması için önerilir.<br /><br />Çıkış ücretlerini kullanmaktan kaçınmak için, birincil hesapla aynı bölgede ikinci depolama hesabı oluşturun. | Birincil hesap herhangi bir şekilde tehlikeye girerse, veriler ikinci depolama hesabından geri yüklenebilir. | AzCopy ve Azure Data Factory desteklenir.<br /><br />Nesne çoğaltma desteklenmiyor. |

## <a name="data-protection-by-resource-type"></a>Kaynak türüne göre veri koruma

Aşağıdaki tabloda, korudukları kaynaklara göre Azure Storage veri koruma seçenekleri özetlenmektedir.

| Veri koruma seçeneği | Bir hesabı silinmeye karşı korur | Kapsayıcıyı silinmeye karşı korur | Blobun silmeyi korur | Bir Blobun üzerine yazılmasına karşı koruma sağlar |
|--|--|--|--|--|
| Azure Resource Manager kilidi | Yes | Hayır<sup>1</sup> | Hayır | Hayır |
| Bir kapsayıcıda dengeszlik kullanılabilirliği ilkesi | Evet<sup>2</sup> | Yes | Yes | Yes |
| Kapsayıcı geçici silme | Hayır | Yes | Hayır | Hayır |
| Blob sürüm oluşturma<sup>3</sup> | Hayır | Hayır | Yes | Yes |
| Blob geçici silme<sup>3</sup> | Hayır | Hayır | Yes | Yes |
| Zaman içinde bir noktaya geri yükleme<sup>3</sup> | Hayır | Hayır | Yes | Yes |
| Blob anlık görüntüsü | Hayır | Hayır | Hayır | Yes |
| İkinci bir hesaba veri kopyalamaya yönelik kendi çözümünüzü Al<sup>4</sup> | Hayır | Yes | Yes | Yes |

<sup>1</sup> Azure Resource Manager bir kilit, kapsayıcıyı silinmeye karşı korumaz.<br />
<sup>2</sup> bir kapsayıcı için yasal bir saklama veya kilitli zaman tabanlı bekletme ilkesi etkin olsa da, depolama hesabı da silinmeye karşı korunur.<br />
<sup>3</sup> Data Lake Storage iş yükleri için şu anda desteklenmiyor.<br />
<sup>4</sup> azcopy ve Azure Data Factory, hem BLOB depolama hem de Data Lake Storage iş yükleri için desteklenen seçeneklerdir. Nesne çoğaltma yalnızca BLOB depolama iş yükleri için desteklenir.<br />

## <a name="recover-deleted-or-overwritten-data"></a>Silinen veya üzerine yazılan verileri kurtar

Üzerine yazılmış veya silinmiş verileri kurtarmanız gerekiyorsa, ne kadar devam ettiğiniz, hangi veri koruma seçeneklerine ve hangi kaynağın etkilendiğine bağlıdır. Aşağıdaki tablo, verileri kurtarmak için gerçekleştirebileceğiniz eylemleri açıklar.

| Kaynak silindi veya üzerine yazıldı | Olası kurtarma eylemleri | Kurtarma gereksinimleri |
|--|--|--|
| Depolama hesabı | Silinen depolama hesabını kurtarma girişimi<br />[Daha fazla bilgi edinin...](../common/storage-account-recover.md) | Depolama hesabı başlangıçta Azure Resource Manager dağıtım modeliyle oluşturuldu ve son 14 gün içinde silindi. Özgün hesap silindiğinden aynı ada sahip yeni bir depolama hesabı oluşturulmamış. |
| Kapsayıcı | Geçici olarak silinen kapsayıcıyı ve içeriğini kurtarma<br />[Daha fazla bilgi edinin...](soft-delete-container-enable.md) | Kapsayıcı geçici silme etkin ve kapsayıcı geçici silme Bekletme dönemi henüz sona ermedi. |
| Kapsayıcılar ve bloblar | İkinci bir depolama hesabından verileri geri yükleme | Tüm kapsayıcı ve BLOB işlemleri, ikinci bir depolama hesabına etkin bir şekilde çoğaltıldı. |
| Blob (herhangi bir tür) | Bir blobu önceki bir sürümden geri yükleme<sup>1</sup><br />[Daha fazla bilgi edinin...](versioning-enable.md) | Blob sürümü oluşturma etkin ve blob bir veya daha fazla önceki sürüme sahip. |
| Blob (herhangi bir tür) | Geçici olarak silinen bir blobu kurtar<sup>1</sup><br />[Daha fazla bilgi edinin...](soft-delete-blob-enable.md) | Blob geçici silme etkin ve geçici silme bekletme aralığı sona ermedi. |
| Blob (herhangi bir tür) | Bir bloba anlık görüntüden geri yükleme<br />[Daha fazla bilgi edinin...](snapshots-manage-dotnet.md) | Blobun bir veya daha fazla anlık görüntüsü vardır. |
| Blok Blobları kümesi | Blok bloblarını bir kümesini daha önceki bir noktada durumuna kurtar<sup>1</sup><br />[Daha fazla bilgi edinin...](point-in-time-restore-manage.md) | Zaman içinde bir noktaya geri yükleme etkinleştirilir ve geri yükleme noktası bekletme aralığının içindedir. Depolama hesabının güvenliği aşılmış veya bozuk. |
| Blob sürümü | Geçici olarak silinen sürüm 1 '<sup>i</sup> kurtarma<br />[Daha fazla bilgi edinin...](soft-delete-blob-enable.md) | Blob geçici silme etkin ve geçici silme bekletme aralığı sona ermedi. |

<sup>1</sup> Data Lake Storage iş yükleri için şu anda desteklenmiyor.

## <a name="summary-of-cost-considerations"></a>Maliyet konularının Özeti

Aşağıdaki tabloda, bu kılavuzda açıklanan çeşitli veri koruma seçeneklerine yönelik maliyet konuları özetlenmektedir.

| Veri koruma seçeneği | Maliyetle ilgili konular |
|-|-|
| Depolama hesabı için Azure Resource Manager kilidi | Depolama hesabında bir kilit yapılandırmak için ücret alınmaz. |
| Bir kapsayıcıda dengeszlik kullanılabilirliği ilkesi | Kapsayıcıda bir değişikliğe karşı kullanılabilirlik ilkesi yapılandırma ücreti alınmaz. |
| Kapsayıcı geçici silme | Depolama hesabı için kapsayıcı geçici silmeyi etkinleştirme ücreti yok. Geçici olarak silinen kapsayıcıda bulunan veriler, geçici olarak silinen kapsayıcı kalıcı olarak silinene kadar etkin verilerle aynı hızda faturalandırılır. |
| Blob sürümü oluşturma | Depolama hesabı için blob sürüm oluşturmayı etkinleştirme ücreti yok. Blob sürümü oluşturma etkinleştirildikten sonra, hesaptaki bir blobdaki her yazma veya silme işlemi yeni bir sürüm oluşturur ve bu da artan kapasite maliyetlerine neden olabilir.<br /><br />Blob sürümü, benzersiz bloklar veya sayfalara göre faturalandırılır. Bu nedenle, temel blob belirli bir sürümden ayrılan için maliyetler artar. Bir blob veya blob sürümünün katmanının değiştirilmesi, bir faturalandırma etkiye sahip olabilir. Daha fazla bilgi için bkz. [fiyatlandırma ve faturalandırma](versioning-overview.md#pricing-and-billing).<br /><br />Maliyetleri denetlemek için gereken eski sürümleri silmek için yaşam döngüsü yönetimi 'ni kullanın. Daha fazla bilgi için bkz. [Azure Blob depolama erişim katmanlarını otomatikleştirerek maliyetleri iyileştirme](storage-lifecycle-management-concepts.md). |
| Blob geçici silme | Depolama hesabı için blob geçici silmeyi etkinleştirme ücreti yok. Geçici olarak silinen blob 'daki veriler, geçici olarak silinen blob kalıcı olarak silinene kadar etkin verilerle aynı hızda faturalandırılır. |
| Belirli bir noktaya geri yükleme | Bir depolama hesabı için zaman içinde geri yüklemeyi etkinleştirme ücreti yoktur; Ancak, zaman içinde geri yüklemeyi etkinleştirmek, blob sürümü oluşturma, geçici silme ve değişiklik akışını, her birinin ek ücretlere neden olabilecek şekilde etkinleştirir.<br /><br />Geri yükleme işlemi gerçekleştirdiğinizde, zaman içinde geri yükleme için faturalandırılırsınız. Geri yükleme işleminin maliyeti, geri yüklenmekte olan veri miktarına bağlıdır. Daha fazla bilgi için bkz. [fiyatlandırma ve faturalandırma](point-in-time-restore-overview.md#pricing-and-billing). |
| Blob anlık görüntüleri | Anlık görüntüdeki veriler, benzersiz bloklar veya sayfalara göre faturalandırılır. Bu nedenle, temel blob anlık görüntüden ayrılan için maliyetler artar. Bir Blobun veya anlık görüntünün katmanının değiştirilmesi, bir faturalandırma etkiye sahip olabilir. Daha fazla bilgi için bkz. [fiyatlandırma ve faturalandırma](snapshots-overview.md#pricing-and-billing).<br /><br />Maliyetleri denetlemek için gerektiğinde eski anlık görüntüleri silmek için yaşam döngüsü yönetimi 'ni kullanın. Daha fazla bilgi için bkz. [Azure Blob depolama erişim katmanlarını otomatikleştirerek maliyetleri iyileştirme](storage-lifecycle-management-concepts.md). |
| Verileri ikinci bir depolama hesabına Kopyala | Verilerin ikinci bir depolama hesabında saklanması, kapasite ve işlem maliyetleri doğurur. İkinci depolama hesabı, kaynak hesaptan farklı bir bölgede bulunuyorsa, verileri bu ikinci hesaba kopyalamak ek olarak çıkış ücretlerine tabi olur. |

## <a name="disaster-recovery"></a>Olağanüstü durum kurtarma

Azure depolama, geçici donanım arızaları, ağ veya güç kesintileri ve çok büyük doğal felaketler de dahil olmak üzere planlı ve planlanmamış olaylardan korunabilmesi için verilerinizin birden çok kopyasını her zaman tutar. Artıklık, depolama hesabınızın, başarısızlık durumunda bile kullanılabilirlik ve dayanıklılık hedeflerini karşıladığından emin olmanızı sağlar. Depolama hesabınızı yüksek kullanılabilirlik için yapılandırma hakkında daha fazla bilgi için bkz. [Azure Storage yedekliği](../common/storage-redundancy.md).

Bir veri merkezinde hata oluşması durumunda, depolama hesabınız iki coğrafi bölgede (coğrafi olarak yedekli) artıkmış ise, hesabınızı birincil bölgeden ikincil bölgeye devretmek için bir seçeneğe sahip olursunuz. Daha fazla bilgi için bkz. [olağanüstü durum kurtarma ve depolama hesabı yük devretme](../common/storage-disaster-recovery-guidance.md).

Müşteri tarafından yönetilen yük devretme, şu anda hiyerarşik bir ad alanı etkinleştirilmiş depolama hesaplarında desteklenmemektedir. Daha fazla bilgi için bkz. [Azure Data Lake Storage 2. bulunan BLOB Storage özellikleri](data-lake-storage-supported-blob-storage-features.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Depolama yedekliliği](../common/storage-redundancy.md)
- [Olağanüstü durum kurtarma ve depolama hesabı yükünü devretme](../common/storage-disaster-recovery-guidance.md)
