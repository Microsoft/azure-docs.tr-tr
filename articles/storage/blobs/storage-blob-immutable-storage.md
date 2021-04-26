---
title: Sabit BLOB depolama-Azure depolama
description: Azure depolama, kullanıcıların belirli bir Aralık için silinebilir olmayan ve değiştirilemeyen bir durumda veri depolamasını sağlayan blob (nesne) depolama için solucan (bir kez yaz, çok oku) desteğini sunar.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: d1d77e508e627520878dcc27b5a643473d11dd1d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800729"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>İş açısından kritik blob verilerini sabit depolama alanıyla depolama

Azure Blob depolama için sabit depolama, kullanıcıların iş açısından kritik veri nesnelerini bir solucan içinde depolamasına olanak sağlar (bir kez yaz, çok oku) durumu. Bu durum, verileri silinebilir olmayan ve Kullanıcı tarafından belirtilen bir Aralık için değiştirilemez hale getirir. Saklama aralığı süresince, Bloblar oluşturulup okunabilir, ancak değiştirilemez veya silinemez. Genel amaçlı v1, genel amaçlı v2, BlobStorage ve tüm Azure bölgelerindeki blok Blobstorage hesapları için sabit depolama kullanılabilir.

Yasal tutmaları ayarlama ve temizleme veya Azure portal, PowerShell veya Azure CLı kullanarak zaman tabanlı bir bekletme ilkesi oluşturma hakkında bilgi için bkz. [BLOB depolama için imlebilirlik Ilkelerini ayarlama ve yönetme](storage-blob-immutability-policies-manage.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="about-immutable-blob-storage"></a>Sabit BLOB depolama hakkında

Sabit depolama, sağlık kurumlarının, mali kurumların ve ilgili sektörlerin &mdash; özellikle de aracı dağıtıcı kuruluşların &mdash; verileri güvenli bir şekilde depolamasına yardımcı olur Sabit depolama Ayrıca, değişiklik veya silme açısından kritik verileri korumak için herhangi bir senaryoda yararlanılabilir olabilir.

Tipik kullanım alanları şunlardır:

- **Mevzuat uyumluluğu**: Azure Blob depolama için sabit depolama, KURULUŞLARıN, sn. a-4 (f), CFTC 1.31 (d), finra ve diğer yönetmelikleri ele masına yardımcı olur. Cohasset ile teknik bir Teknik İnceleme ayrıntıları, bu mevzuat gereksinimlerinin ne kadar sabit olduğunu [Microsoft hizmet güveni portalı](https://aka.ms/AzureWormStorage)aracılığıyla indirileceğine ilişkin ayrıntıları ayrıntılarıyla ilişkilendirir. [Azure Güven Merkezi](https://www.microsoft.com/trustcenter/compliance/compliance-overview) , uyumluluk sertifikalarımızla ilgili ayrıntılı bilgiler içerir.

- **Güvenli belge saklama**: Azure Blob depolama için sabit depolama, verilerin hesap yönetici ayrıcalıklarına sahip kullanıcılar dahil olmak üzere herhangi bir kullanıcı tarafından değiştirilmesini veya silinmesini sağlar.

- **Yasal tutma**: Azure Blob depolama için sabit depolama, kullanıcıların, bekletme kaldırılana kadar istenen süre için, BT veya iş kullanımı açısından kritik öneme sahip gizli bilgileri, istenen süreye göre depolamanıza olanak sağlar. Bu özellik yalnızca yasal kullanım durumları ile sınırlı değildir, ancak olay tetikleyicilerine veya kurumsal ilkeye göre verilerin korunması gerektiğinde olay tabanlı bir saklama veya kurumsal kilit olarak da düşünülebilir.

Sabit depolama aşağıdaki özellikleri destekler:

- **[Zamana dayalı bekletme ilkesi desteği](#time-based-retention-policies)**: kullanıcılar, belirli bir Aralık için verileri depolamak üzere ilkeler ayarlayabilir. Zaman tabanlı bir bekletme ilkesi ayarlandığında, Bloblar oluşturulabilir ve okunabilir, ancak değiştirilemez veya silinemez. Saklama süresi dolduktan sonra Bloblar silinebilir ancak üzerine yazılmaz.

- **[Yasal tutma ilkesi desteği](#legal-holds)**: bekletme aralığı bilinmiyorsa, kullanıcılar yasal tutmaları, yasal saklama temizlenene kadar sabit verileri depolayacak şekilde ayarlayabilir.  Yasal bir saklama ilkesi ayarlandığında, Bloblar oluşturulabilir ve okunabilir, ancak değiştirilemez veya silinemez. Her yasal saklama, bir tanımlayıcı dize olarak kullanılan Kullanıcı tanımlı bir alfasayısal etiketle (örneğin, bir durum KIMLIĞI, olay adı vb.) ilişkilendirilir. 

- **Tüm blob katmanları için destek**: WORM ilkeleri Azure Blob depolama katmanından bağımsızdır ve sık erişimli, seyrek erişimli katmanlara ve arşiv katmanlarına uygulanır. Kullanıcılar verileri iş yüklerine göre en uygun maliyetli katmanda depolarken verilerin de sabit tutulmasını sağlayabilir.

- **Kapsayıcı düzeyinde yapılandırma**: kullanıcılar, zaman tabanlı bekletme ilkelerini ve geçerli saklama etiketlerini kapsayıcı düzeyinde yapılandırabilir. Kullanıcılar basit kapsayıcı düzeyi ayarlarını kullanarak zamana bağlı saklama ilkelerini oluşturup kilitleme, saklama aralıklarını uzatma ve yasal tutma ayarlayıp silme gibi daha birçok işlem yapabilir. Bu ilkeler kapsayıcıdaki mevcut ve yeni tüm bloblara uygulanır. HNS etkin bir hesap için, bu ilkeler bir kapsayıcıdaki tüm dizinlere de uygulanır.

- **Denetim günlüğü desteği**: her kapsayıcı bir ilke Denetim günlüğü içerir. Kilitleme zaman tabanlı bekletme ilkeleri için yedi adede kadar zamana dayalı saklama komutu gösterir ve Kullanıcı KIMLIĞI, komut türü, zaman damgaları ve bekletme aralığını içerir. Yasal tutma olaylarında günlük girişinde kullanıcı kimliği, komut türü, zaman damgaları ve yasal tutma etiketleri yer alır. Bu günlük, SEC 17A-4 (f) mevzuat yönergelerine uygun olarak ilkenin kullanım ömrü boyunca tutulur. [Azure etkinlik günlüğü](../../azure-monitor/essentials/platform-logs-overview.md) tüm denetim düzlemi etkinliklerinin daha kapsamlı bir günlüğünü gösterir; [Azure Kaynak günlüklerinin](../../azure-monitor/essentials/platform-logs-overview.md) etkinleştirilmesi ve veri düzlemi işlemlerini gösterir. Düzenlemeler veya diğer amaçlar doğrultusunda ihtiyaç duyulabilecek günlüklerin düzenli olarak depolanması kullanıcının sorumluluğundadır.

## <a name="how-it-works"></a>Nasıl çalışır?

Azure Blob depolama için sabit depolama özelliği, iki WORM veya sabit ilke türünü destekler: zamana bağlı saklama ve yasal tutma. Bir kapsayıcıya zaman tabanlı bir bekletme ilkesi veya yasal saklama alanı uygulandığında, mevcut tüm Bloblar 30 saniyeden az bir şekilde sabit bir solucan durumuna geçer. Bu ilkeyle korunan kapsayıcıya yüklenen tüm yeni Bloblar da sabit bir duruma geçer. Tüm Bloblar sabit bir durumda olduktan sonra, değişmez ilke onaylanır ve sabit kapsayıcıda üzerine yazma veya silme işlemlerine izin verilmez. HNS etkin bir hesap söz konusu olduğunda, Bloblar yeniden adlandırılamaz veya farklı bir dizine taşınamaz.

Kapsayıcıda, yasal bir saklama veya kilitli zaman tabanlı bir ilke tarafından korunan bir blob varsa kapsayıcı ve depolama hesabı silmeye de izin verilmez. Yasal bir saklama ilkesi blob, kapsayıcı ve depolama hesabı silmeye karşı korunur. Hem kilidi açılmış hem de kilitli zaman tabanlı ilkeler, belirtilen süre boyunca blob silinmeye karşı korunur. Hem kilitlemeli hem de kilitli zaman tabanlı ilkeler, kapsayıcıda yalnızca en az bir blob varsa kapsayıcı silmeye karşı koruma sağlayacaktır. Yalnızca *kilitli* zaman tabanlı ilkeye sahip bir kapsayıcı, depolama hesabı silmelerini karşı korunur; kilidi açılmış zaman tabanlı ilkelerle kapsayıcı, depolama hesabı silme koruması ve uyumluluğu sunmaz.

Aşağıdaki diyagramda, zaman tabanlı bekletme ilkelerinin ve yasal tutma işlemlerinin, etkin durumdayken yazma ve silme işlemlerini nasıl engellediği gösterilmektedir.

:::image type="content" source="media/storage-blob-immutable-storage/worm-diagram.png" alt-text="Bekletme ilkelerinin ve yasal tutma işlemlerinin yazma ve silme işlemlerini nasıl engellediğini gösteren diyagram":::

Zamana dayalı saklama ilkelerini ayarlama ve kilitleme hakkında daha fazla bilgi için bkz. [BLOB depolama için imlebilirlik Ilkelerini ayarlama ve yönetme](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Zamana dayalı saklama ilkeleri

> [!IMPORTANT]
> Bir zaman tabanlı bekletme ilkesi, blob 'un SEC 17A-4 (f) ve diğer yasal *uyumluluk için uyumlu* bir sabit (yazma ve silme korumalı) durumunda olması gerekir. Microsoft, ilkeyi genellikle 24 saatten daha az sürede kilitlemenizi önerir. Uygulanan bir zaman tabanlı bekletme ilkesinin ilk durumu, kilidi kapatmadan önce özelliği test etmeniz ve ilkede değişiklikler *yapmanız sağlanır.* *Kilidi açık* duruma karşı koruma sağlarken, kısa vadeli Özellik denemeleri dışında herhangi bir amaçla *kilitlenmemiş* durum kullanılması önerilmez.
>
> Zaman tabanlı bekletme ilkesi kilitlendiğinde, ilke kaldırılamaz ve etkin saklama süresine en fazla beş artışa izin verilir. Saklama dönemi Azaltılabilecek.

Bir kapsayıcıya zaman tabanlı bir bekletme ilkesi uygulandığında, kapsayıcıdaki tüm Bloblar, *etkin* saklama dönemi süresince sabit durumda kalır. Blob 'lar için geçerli saklama süresi, Blobun **oluşturulma zamanı** ve Kullanıcı tarafından belirtilen bekletme aralığı arasındaki farka eşittir. Kullanıcılar saklama süresini uzatabildiğinden, sabit depolama geçerli olan saklama süresinin hesaplanmasında kullanıcı tarafından belirtilen en son saklama aralığı değeri kullanılır.

Örneğin, bir kullanıcının beş yıl bekletme aralığı ile zaman tabanlı bir bekletme ilkesi oluşturduğunu varsayalım. Bu kapsayıcıda bulunan mevcut bir blob, _testblob1_, bir yıl önce oluşturulmuştur; Bu nedenle, _testblob1_ için geçerli saklama süresi dört yıldır. _Testblob2_ yeni bir blob, kapsayıcıya yüklendiğinde, _testblob2_ için geçerli saklama süresi, oluşturma zamanından beş yıl olur.

Kilitlemeli bir süre tabanlı bekletme ilkesi yalnızca özellik testi için önerilir ve sn, SEC 17A-4 (f) ve diğer mevzuata uyumluluğuyla uyumlu olması için kilitlenmelidir.

Aşağıdaki sınırlar bekletme ilkeleri için geçerlidir:

- Depolama hesabı için, kilitli zamana dayalı sabit ilkelerle maksimum kapsayıcı sayısı 10.000 ' dir.
- En düşük saklama aralığı 1 gündür. En fazla 146.000 gün (400 yıl).
- Bir kapsayıcı için, kilitli zaman tabanlı sabit ilkeler için bir bekletme aralığını uzatmak üzere en fazla düzenleme sayısı 5 ' tir.
- Bir kapsayıcı için, kilitli bir ilke için en fazla yedi zaman tabanlı bekletme ilkesi denetim günlüğü tutulur.

### <a name="allow-protected-append-blobs-writes"></a>Korumalı ekleme Blobları yazmaları sağlar

Ekleme Blobları, veri bloklarından oluşur ve denetim ve günlük senaryoları için gereken veri ekleme işlemleri için en iyi duruma getirilmiştir. Tasarım, ekleme Blobları yalnızca Blobun sonuna yeni blokların eklenmesine izin verir. Değişiklik yapılarından bağımsız olarak, bir ekleme blobu içindeki mevcut blokların değiştirilmesine veya silinmesine göre temelde izin verilmez. Blob ekleme hakkında daha fazla bilgi için bkz. [BLOB ekleme blobu hakkında](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Yalnızca zaman tabanlı bekletme ilkeleri `allowProtectedAppendWrites` , daha fazla koruma ve uyumluluk sağlarken, ek bir bloba yeni bloklar yazmaya olanak tanıyan bir ayara sahiptir. Bu ayar etkinleştirilirse, ilkeyle korunan kapsayıcıda doğrudan bir ekleme blobu oluşturabilir ve *Appendblock* API 'sini kullanarak var olan ekleme bloblarının sonuna yeni veri blokları eklemeye devam edebilirsiniz. Yalnızca yeni bloklar eklenebilir ve var olan tüm bloklar değiştirilemez veya silinemez. Zaman bekletme güvenilirlik koruması hala geçerlidir, etkin saklama süresi geçene kadar ekleme blobu silmeyi önler. Bu ayarın etkinleştirilmesi, blok Blobları veya sayfa Blobları için dengesterlebilirlik davranışını etkilemez.

Bu ayar, zaman tabanlı bekletme ilkesinin bir parçası olduğundan, ekleme Blobları, *etkin* saklama dönemi süresince hala sabit durumda kalır. Yeni veriler ekleme Blobun ilk oluşturulduktan sonra eklenebileceği için, bekletme döneminin nasıl belirlendiği küçük bir farklılık vardır. Etkin saklama, ekleme blobunun **son değiştirilme zamanı** ve Kullanıcı tarafından belirtilen bekletme aralığı arasındaki farktır. Benzer şekilde, saklama aralığı genişletildiğinde, sabit depolama, etkin saklama süresini hesaplamak için Kullanıcı tarafından belirtilen bekletme aralığının en son değerini kullanır.

Örneğin, bir kullanıcının etkin bir zaman tabanlı bekletme ilkesi oluşturduğunu `allowProtectedAppendWrites` ve 90 günlük bekletme aralığına sahip olduğunu varsayalım. Bir Append blobu, _logblob1_, bugün kapsayıcıda oluşturulur, yeni Günlükler sonraki 10 gün boyunca ekleme blobuna eklenmeye devam eder; Bu nedenle, _logblob1_ için geçerli saklama süresi bugünden itibaren 100 gündür (son append + 90 gün saati).

Kilitsiz zaman tabanlı bekletme ilkeleri, `allowProtectedAppendWrites` ayarın etkin ve devre dışı olarak herhangi bir zamanda etkinleştirilmesini sağlar. Zaman tabanlı bekletme ilkesi kilitlendiğinde, `allowProtectedAppendWrites` ayar değiştirilemez.

Yasal saklama ilkeleri etkinleştirilemez `allowProtectedAppendWrites` ve geçerli saklama, ' allowProtectedAppendWrites ' özelliğini null olarak tutacaktır. Etkin olan bir zaman tabanlı bekletme ilkesine geçerli bir saklama uygulanmışsa `allowProtectedAppendWrites` , yasal tutmaya yükseltilmemiş kadar *Appendblock* API 'si başarısız olur.

## <a name="legal-holds"></a>Yasal tutma

Yasal saklama işlemleri, yasal Araştırma amaçları veya genel koruma ilkeleri için kullanılabilen geçici tutmalar. Her yasal saklama ilkesinin bir veya daha fazla etiketle ilişkilendirilmesi gerekir. Etiketler, bekletme amacını kategorilere ayırmak ve belirlemek için bir Case ID veya Event gibi adlandırılmış bir tanımlayıcı olarak kullanılır.

Bir kapsayıcı aynı anda hem yasal ayrı tutmaya hem de zaman tabanlı bir bekletme ilkesine sahip olabilir. Geçerli saklama süresi sona erse dahi tüm yasal tutma durumları kaldırılana kadar kapsayıcı içindeki tüm bloblar sabit durumda kalır. Buna karşın tüm yasal tutma ilkeleri silinse dahi geçerli olan saklama süresi boyunca blob sabit durumda kalır.

Geçerli tutmalar için aşağıdaki sınırlar geçerlidir:

- Bir depolama hesabı için, geçerli bir saklama ayarı olan en fazla kapsayıcı sayısı 10.000 ' dir.
- Bir kapsayıcı için, en fazla yasal saklama etiketi sayısı 10 ' dur.
- Geçerli bir saklama etiketinin minimum uzunluğu üç alfasayısal karakterdir. Maksimum uzunluk 23 alfasayısal karakterdir.
- Bir kapsayıcı için, ilke süresince en fazla 10 yasal saklama ilkesi denetim günlüğü tutulur.

## <a name="scenarios"></a>Senaryolar

Aşağıdaki tabloda, farklı sabit senaryolar için devre dışı bırakılmış BLOB depolama işlemlerinin türleri gösterilmektedir. Daha fazla bilgi için bkz. [Azure Blob hizmeti REST API](/rest/api/storageservices/blob-service-rest-api) belgeleri.

| Senaryo | Blob durumu | Blob işlemleri reddedildi | Kapsayıcı ve hesap koruması |
|--|--|--|--|
| Blobdaki geçerli saklama süresi dolmadı ve/veya yasal tutma ayarlandı | Sabit: hem silme hem de yazma korumalı | Blob<sup>1</sup>, put bloğu<sup>1</sup>, yerleştirme, blok listesi<sup>1</sup>, kapsayıcıyı silme, blobu silme, blob meta verilerini ayarlama, yerleştirme sayfası, blob özelliklerini ayarlama, anlık görüntü blobu, artımlı kopya blobu, ekleme bloğu<sup>2</sup> | Kapsayıcı silme reddedildi; Depolama hesabı silme reddedildi |
| Blobda etkin bekletme aralığının süresi doldu ve geçerli bir tutma ayarlanmadı | Yalnızca yazma korumalı (silme işlemlerine izin verilir) | Blob<sup>1</sup>, put bloğu<sup>1</sup>, yerleştirme, blok listesi<sup>1</sup>, blob meta verilerini ayarlama, yerleştirme sayfası, blob özelliklerini ayarlama, anlık görüntü blobu, artımlı kopya blobu, ekleme bloğu<sup>2</sup> | Korumalı kapsayıcıda en az 1 blob varsa kapsayıcı silme reddedildi; Depolama hesabı silme yalnızca *kilitli* zaman tabanlı ilkeler için reddedildi |
| Hiçbir solucan ilkesi uygulanmadı (zaman tabanlı saklama ve geçerli saklama etiketi yok) | Değiştirilebilir | Yok | Yok |

<sup>1</sup> blob hizmeti bu işlemlerin bir kez yeni blob oluşturmasına izin verir. Sabit bir kapsayıcıda var olan bir blob yolundaki tüm sonraki üzerine yazma işlemlerine izin verilmez.

<sup>2</sup> Append bloğuna yalnızca özelliği etkinleştirilmiş zaman tabanlı bekletme ilkeleri için izin verilir `allowProtectedAppendWrites` . Daha fazla bilgi için, [korumalı ekleme Blobları yazmaları Izin ver](#allow-protected-append-blobs-writes) bölümüne bakın.

> [!IMPORTANT]
> [URL 'ye SQL yedeklemesi](/sql/relational-databases/backup-restore/sql-server-backup-to-url)gibi bazı iş yükleri bir blob oluşturup daha sonra buna ekler. Kapsayıcıda etkin bir zaman tabanlı bekletme ilkesi veya yasal tutma varsa, bu model başarılı olmaz.

## <a name="pricing"></a>Fiyatlandırma

Bu özelliği kullanmak için ek ücret alınmaz. Değişmez veriler, değişebilir verilerle aynı şekilde fiyatlandırılır. Azure Blob depolama hakkındaki fiyatlandırma ayrıntıları için bkz. [Azure Depolama fiyatlandırması sayfası](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>SSS

**SOLUCAN uyumluluğuna ilişkin belgeler verebilir misiniz?**

Evet. Microsoft, uyumluluğu belgelemek için, sabit blob depolamayı ve finans hizmetleri sektörüne özgü gereksinimlerle uyumluluğunu değerlendirmek üzere kayıt yönetimi ve bilgi yönetimi, Cohasset Associates ' de uzmanlaşmış, önde gelen bağımsız bir değerlendirme firması elde eder. Cohasset, bir solucan durumunda zaman tabanlı Blobları sürdürmek için kullanıldığında, sabit BLOB depolama alanının, CFTC Rule 1.31 (c)-(d), FINRA kuralı 4511 ve SEC Rule 17A-4 ' nin ilgili depolama gereksinimlerini karşılar. Microsoft, bu kural kümesini hedefleyen, mali kurumlara yönelik kayıtlar için küresel olarak en iyi şekilde koruma kılavuzunu temsil etmektedir. Cohasset raporu, [Microsoft hizmet Güven Merkezi](https://aka.ms/AzureWormStorage)' nde kullanılabilir. Microsoft 'tan solucan kanıtlama uygunluk uyumluluğuna ilişkin bir kanıtlama mektubu istemek için lütfen Azure desteğine başvurun.

**Özellik yalnızca blob 'ları ve ekleme bloblarını ve sayfa bloblarını da engellemek için geçerlidir mi?**

Sabit depolama, kapsayıcı düzeyinde ayarlandığı için herhangi bir blob türüyle kullanılabilir, ancak genellikle blok bloblarını ve ekleme bloblarını depolayan kapsayıcılar için solucan kullanmanızı öneririz. Bir kapsayıcıdaki mevcut Bloblar, yeni set SOLUCANı ilkesiyle korunacaktır. Ancak yeni sayfa bloblarının solucan kapsayıcısının dışında oluşturulması ve ardından içinde kopyalanması gerekir. Bir solucan kapsayıcısına kopyalandıktan sonra, bir sayfa blobuna başka değişikliklere izin verilmez. VM diskini kilitleyecek şekilde VHD 'leri (sayfa Blobları) depolayan bir kapsayıcıda bir solucan ilkesi ayarlamak önerilmez. Zaman tabanlı herhangi bir ilkeyi kilitlemeden önce, belgeleri iyice incelemenizi ve senaryolarınızı test etmenizi öneririz.

**Bu özelliği kullanmak için yeni bir depolama hesabı oluşturmem gerekir mi?**

Hayır, mevcut veya yeni oluşturulan genel amaçlı v1, genel amaçlı v2, BlobStorage veya BlockBlobStorage hesaplarıyla sabit depolama kullanabilirsiniz. Genel amaçlı v1 depolama hesapları desteklenir, ancak daha fazla özellikten faydalanabilmeniz için, genel amaçlı v2 'ye yükseltmeniz önerilir. Mevcut bir genel amaçlı v1 depolama hesabını yükseltme hakkında daha fazla bilgi için bkz. [Depolama hesabını yükseltme](../common/storage-account-upgrade.md).

**Yalnızca yasal bir saklama ve zaman tabanlı bekletme ilkesi uygulayabilir miyim?**

Evet, bir kapsayıcı aynı anda hem yasal ayrı tutmaya hem de zaman tabanlı bir bekletme ilkesine sahip olabilir; Ancak, geçerli saklama temizlenene kadar ' allowProtectedAppendWrites ' ayarı geçerli olmayacaktır. Geçerli saklama süresi sona erse dahi tüm yasal tutma durumları kaldırılana kadar kapsayıcı içindeki tüm bloblar sabit durumda kalır. Buna karşın tüm yasal tutma ilkeleri silinse dahi geçerli olan saklama süresi boyunca blob sabit durumda kalır. 

**Yasal saklama ilkeleri yalnızca yasal bir şekilde geçerlidir veya başka kullanım senaryolarında bulunabilir mi?**

Hayır, yasal tutma yalnızca zamana bağlı olmayan bir bekletme ilkesi için kullanılan genel bir terimdir. Yalnızca, ile ilgili bir yaklaşım için kullanılması gerekmez. Yasal saklama ilkeleri, bekletme döneminin bilinmediği önemli kurumsal solucan verilerini korumak için üzerine yazma ve silmeleri devre dışı bırakmak için yararlıdır. Görev açısından kritik solucan iş yüklerinizi korumak için bir kuruluş ilkesi olarak kullanabilir veya özel bir olay tetikleyicisi zaman tabanlı bir bekletme ilkesi kullanımını gerektirdiğinden bunu bir hazırlama ilkesi olarak kullanabilirsiniz. 

**_Kilitli_ bir zaman tabanlı bekletme ilkesi veya yasal tutmayı kaldırabilir miyim?**

Yalnızca kilitlenmemiş süre tabanlı bekletme ilkeleri bir kapsayıcıdan kaldırılabilir. Zaman tabanlı bekletme ilkesi kilitlendikten sonra kaldırılamaz; Yalnızca etkili Bekletme dönemi uzantılarına izin verilir. Yasal saklama etiketleri silinebilir. Tüm yasal Etiketler silindiğinde, yasal saklama kaldırılır.

**Zaman tabanlı bekletme ilkesiyle veya yasal tutmayla kapsayıcıyı silmeye çalışırsam ne olur?**

Kapsayıcıda en az bir blob varsa, kilitli veya kilitlenmemiş bir zaman tabanlı bekletme ilkesiyle veya kapsayıcıda geçerli bir saklama varsa, kapsayıcı silme işlemi başarısız olur. Kapsayıcıyı silme işlemi, yalnızca kapsayıcıda blob yoksa ve geçerli tutma yoksa başarılı olur. 

**Zaman tabanlı bekletme ilkesine veya yasal tutmaya sahip bir kapsayıcıya sahip bir depolama hesabını silmeye çalışırsam ne olur?**

Geçerli bir saklama kümesi veya **kilitli** zaman tabanlı bir ilke olan en az bir kapsayıcı varsa, depolama hesabı silme işlemi başarısız olur. Kilidi açılmış zaman tabanlı ilkeye sahip bir kapsayıcı, depolama hesabı silinmeye karşı koruma sağlamaz. Depolama hesabını silebilmeniz için tüm yasal tutmaları kaldırmanız ve tüm **kilitli** kapsayıcıları silmeniz gerekir. Kapsayıcı silme hakkında daha fazla bilgi için önceki soruya bakın. Ayrıca, [Azure Resource Manager kilitleri](../../azure-resource-manager/management/lock-resources.md)olan depolama hesabınıza yönelik daha fazla silme koruması da uygulayabilirsiniz.

**Blob sabit durumundayken verileri farklı blob katmanlarında (sık erişimli, seyrek erişimli Arşiv) taşıyabilir miyim?**

Evet, verileri uyumlu sabit durumda tutarken blob katmanlarında verileri taşımak için blob katmanını ayarla komutunu kullanabilirsiniz. Sabit depolama, sık erişimli, seyrek erişimli ve arşiv blob katmanlarında desteklenir.

**Ödemeyi yapamadıysam ve saklama süresi dolmadıysa ne olur?**

Ödeme dışı durumda, normal veri saklama ilkeleri Microsoft ile sözleşmenizin hüküm ve koşullarından dolayı geçerli olur. Genel bilgi için bkz. [Microsoft 'Ta veri yönetimi](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**Özelliği deneyebilmek için deneme veya yetkisiz kullanım süresi sunuyor musunuz?**

Evet. Zaman tabanlı bir bekletme ilkesi ilk oluşturulduğunda, *kilidi açılmış* durumda olur. Bu durumda, bekletme aralığında (artış veya azaltma, hatta ilkeyi silme gibi) istediğiniz değişikliği yapabilirsiniz. İlke kilitlendikten sonra, saklama aralığı sona erene kadar kilitli kalır. Bu kilitli ilke, saklama aralığına silme ve değiştirme yapılmasını engeller. *Kilidi açık* durumdan yalnızca deneme amacıyla kullanmanızı ve ilkeyi 24 saatlik bir süre içinde kilitlemeyi önemle öneririz. Bu uygulamalar, SEC 17A-4 (f) ve diğer yönetmeliklere uyum sağlamanıza yardımcı olur.

**Sabit blob ilkeleriyle birlikte geçici silme kullanabilir miyim?**

Evet, uyumluluk gereksinimleriniz geçici silmenin etkinleştirilmesini sağlar. [Azure Blob depolama Için geçici silme](./soft-delete-blob-overview.md) , yasal bir saklama veya zaman tabanlı bekletme ilkesinden bağımsız olarak bir depolama hesabındaki tüm kapsayıcılar için geçerlidir. Herhangi bir sabit solucan İlkesi uygulanmadan ve onaylanmadan önce ek koruma için geçici silme özelliğinin etkinleştirilmesini öneririz.

**HNS etkin bir hesap için blob sabit durumundayken bir blobu yeniden adlandırabilir veya taşıyabilir miyim?**
Hayır, hem ad hem de dizin yapısı, sabit ilke yerleştirildikten sonra değiştirilemeyen önemli kapsayıcı düzeyi veriler olarak değerlendirilir. Yeniden adlandırma ve taşıma yalnızca, genel olarak yalnızca HNS özellikli hesaplar için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [BLOB depolama için dengesde kullanılabilirlik ilkelerini ayarlama ve yönetme](storage-blob-immutability-policies-manage.md)
- [Yaşam döngüsü yönetimi ile blob verilerini otomatik olarak katmana ve silmeye yönelik kurallar ayarlama](storage-lifecycle-management-concepts.md)
- [Azure Depolama blobları için geçici silme](./soft-delete-blob-overview.md)
- [Abonelikleri, kaynak gruplarını ve kaynakları Azure Resource Manager kilitlerle koruyun](../../azure-resource-manager/management/lock-resources.md).
