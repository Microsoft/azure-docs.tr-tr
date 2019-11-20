---
title: Azure depolama Blobları için sabit depolama | Microsoft Docs
description: Azure depolama, kullanıcıların belirli bir Aralık için silinebilir olmayan ve değiştirilemeyen bir durumda veri depolamasını sağlayan blob (nesne) depolama için solucan (bir kez yaz, çok oku) desteğini sunar.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 9caa63972c58defe2e8e2b33b6c2d29b15c7ce84
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168387"
---
# <a name="store-business-critical-data-in-azure-blob-storage-immutably"></a>İş açısından kritik verileri Azure Blob depolama 'da daha duyarlı olarak depolayın 

Azure Blob depolama için sabit depolama, kullanıcıların iş açısından kritik veri nesnelerini bir solucan içinde depolamasına olanak sağlar (bir kez yaz, çok oku) durumu. Bu durum, verileri silinebilir olmayan ve Kullanıcı tarafından belirtilen bir Aralık için değiştirilemez hale getirir. Blob nesneleri, bekletme aralığı süresince oluşturulabilir ve okunabilir, ancak değiştirilemez veya silinemez. Genel Amaçlı v2 ve tüm Azure bölgelerinde BLOB depolama hesapları için sabit depolama etkindir.

## <a name="overview"></a>Genel Bakış

Sabit depolama, sağlık kurumlarının, mali kurumların ve ilgili sektörlerin (özellikle de Broker-dağıtıkurumları) güvenli bir şekilde depolanmasını sağlar. Ayrıca, değişiklik veya silme açısından kritik verileri korumak için herhangi bir senaryoda yararlanılabilir de olabilir. 

Tipik kullanım alanları şunlardır:

- **Mevzuat uyumluluğu**: Azure Blob depolama için sabit depolama, KURULUŞLARıN, sn. a-4 (f), CFTC 1.31 (d), finra ve diğer yönetmelikleri ele masına yardımcı olur. Cohasset ile teknik bir Teknik İnceleme ayrıntıları, bu mevzuat gereksinimlerinin ne kadar sabit olduğunu [Microsoft hizmet güveni portalı](https://aka.ms/AzureWormStorage)aracılığıyla indirileceğine ilişkin ayrıntıları ayrıntılarıyla ilişkilendirir. [Azure Güven Merkezi](https://www.microsoft.com/trustcenter/compliance/compliance-overview) , uyumluluk sertifikalarımızla ilgili ayrıntılı bilgiler içerir.

- **Güvenli belge saklama**: Azure Blob depolama için sabit depolama, verilerin hesap yönetici ayrıcalıklarına sahip kullanıcılar dahil olmak üzere herhangi bir kullanıcı tarafından değiştirilmesini veya silinmesini sağlar.

- **Yasal tutma**: Azure Blob depolama için sabit depolama, kullanıcıların, bekletme kaldırılana kadar istenen süre için, BT veya iş kullanımı açısından kritik öneme sahip gizli bilgileri, istenen süreye göre depolamanıza olanak sağlar. Bu özellik yalnızca yasal kullanım durumları ile sınırlı değildir, ancak olay tetikleyicilerine veya kurumsal ilkeye göre verilerin korunması gerektiğinde olay tabanlı bir saklama veya kurumsal kilit olarak da düşünülebilir.

Sabit depolama şunları destekler:

- **[Zamana dayalı bekletme ilkesi desteği](#time-based-retention)** : kullanıcılar, belirli bir Aralık için verileri depolamak üzere ilkeler ayarlayabilir. Zaman tabanlı bir bekletme ilkesi ayarlandığında, Bloblar oluşturulabilir ve okunabilir, ancak değiştirilemez veya silinemez. Saklama süresi dolduktan sonra Bloblar silinebilir ancak üzerine yazılmaz.

- **[Yasal tutma ilkesi desteği](#legal-holds)** : bekletme aralığı bilinmiyorsa, kullanıcılar yasal saklama süresi temizlenene kadar verileri uygun bir şekilde depolayacak şekilde ayarlayabilir.  Yasal bir saklama ilkesi ayarlandığında, Bloblar oluşturulabilir ve okunabilir, ancak değiştirilemez veya silinemez. Her yasal saklama, bir tanımlayıcı dize olarak kullanılan Kullanıcı tanımlı bir alfasayısal etiketle (örneğin, bir durum KIMLIĞI, olay adı vb.) ilişkilendirilir. 

- **Tüm blob katmanları Için destek**: solucan Ilkeleri Azure Blob depolama katmanından bağımsızdır ve tüm katmanlara uygulanır: sık erişimli, seyrek erişimli ve arşiv. Kullanıcılar iş yükleri için en uygun maliyetli katmana veri geçirebilir, bu da veri dengeszliği sağlar.

- **Kapsayıcı düzeyinde yapılandırma**: kullanıcılar, zaman tabanlı bekletme ilkelerini ve geçerli saklama etiketlerini kapsayıcı düzeyinde yapılandırabilir. Kullanıcılar, basit kapsayıcı düzeyi ayarları kullanarak zaman tabanlı bekletme ilkeleri oluşturup kilitleyebilir, bekletme aralıklarını genişletebilir, yasal tutmaları ayarlayabilir ve temizleyebilir ve daha fazlasını yapabilir. Bu ilkeler, kapsayıcıdaki tüm Bloblar, hem mevcut hem de yeni için geçerlidir.

- **Denetim günlüğü desteği**: her kapsayıcı bir ilke Denetim günlüğü içerir. Kilitleme zaman tabanlı bekletme ilkeleri için yedi adede kadar zamana dayalı saklama komutu gösterir ve Kullanıcı KIMLIĞI, komut türü, zaman damgaları ve bekletme aralığını içerir. Yasal tutar için, günlük Kullanıcı KIMLIĞI, komut türü, zaman damgaları ve yasal saklama etiketlerini içerir. Bu günlük, SEC 17A-4 (f) mevzuat yönergelerine uygun olarak ilkenin kullanım ömrü boyunca tutulur. [Azure etkinlik günlüğü](../../azure-monitor/platform/activity-logs-overview.md) tüm denetim düzlemi etkinliklerinin daha kapsamlı bir günlüğünü gösterir; [Azure tanılama günlüklerini](../../azure-monitor/platform/resource-logs-overview.md) etkinleştirme, veri düzlemi işlemlerini korur ve gösterir. Yasal bir şekilde veya başka amaçlar için gerekli olabileceğinden, bu günlükleri kalıcı olarak depolamak kullanıcının sorumluluğundadır.

## <a name="how-it-works"></a>Nasıl çalışır?

Azure Blob depolama için sabit depolama, iki tür SOLUCANı veya sabit ilkeyi destekler: zamana dayalı bekletme ve yasal tutar. Bir kapsayıcıya zaman tabanlı bir bekletme ilkesi veya yasal saklama alanı uygulandığında, mevcut tüm Bloblar 30 saniyeden az bir şekilde sabit bir solucan durumuna geçer. Bu kapsayıcıya yüklenen tüm yeni Bloblar da sabit duruma geçer. Tüm Bloblar sabit duruma taşındıktan sonra, değişmez ilke onaylanır ve sabit kapsayıcıda var olan ve yeni nesneler için tüm üzerine yazma veya silme işlemlerine izin verilmez.

Sabit bir ilke tarafından korunan blob 'lar varsa kapsayıcı ve hesap silmeye de izin verilmez. Kilitli zaman tabanlı bekletme ilkesine veya yasal tutmaya sahip en az bir blob varsa, kapsayıcıyı silme işlemi başarısız olur. Yasal tutma ilkesine veya zaman tabanlı saklama aralığına sahip en az bir WORM kapsayıcısı olması halinde depolama hesabı silme işlemi başarısız olur. 

### <a name="time-based-retention"></a>Zamana dayalı saklama

> [!IMPORTANT]
> Bir zaman tabanlı bekletme ilkesi, blob 'un SEC 17A-4 (f) ve diğer yasal *uyumluluk için uyumlu* bir sabit (yazma ve silme korumalı) durumunda olması gerekir. İlkeyi, genellikle 24 saatten daha az sürede kilitlemenizi öneririz. Uygulanan bir zaman tabanlı bekletme ilkesinin ilk durumu, kilidi kapatmadan önce özelliği test etmeniz ve ilkede değişiklikler *yapmanız sağlanır.* *Kilidi açık* duruma karşı koruma sağlarken, kısa vadeli Özellik denemeleri dışında herhangi bir amaçla *kilitsiz* durum kullanılması önerilmez. 

Bir kapsayıcıya zaman tabanlı bir bekletme ilkesi uygulandığında, kapsayıcıdaki tüm Bloblar, *etkin* saklama dönemi süresince sabit durumda kalır. Mevcut bloblar için geçerli olan saklama süresi, blob oluşturma zamanı ile kullanıcı tarafından belirtilen saklama aralığı arasındaki farka eşittir.

Yeni bloblar için geçerli olan saklama süresi, kullanıcı tarafından belirtilen saklama aralığına eşittir. Kullanıcılar bekletme aralığını genişletebildiğinden, sabit depolama, etkin saklama süresini hesaplamak için Kullanıcı tarafından belirtilen bekletme aralığının en son değerini kullanır.

> [!TIP]
> **Örnek:** Kullanıcı, beş yıla ait bekletme aralığı ile zaman tabanlı bir bekletme ilkesi oluşturur.
>
> Bu kapsayıcıda bulunan mevcut blob, _testblob1_, bir yıl önce oluşturulmuş. _Testblob1_ için geçerli saklama süresi dört yıldır.
>
> Yeni bir blob olan _testblob2_, artık kapsayıcıya karşıya yüklendi. Bu yeni blob için geçerli saklama süresi beş yıldır.

Kilitlemeli bir süre tabanlı bekletme ilkesi yalnızca özellik testi için önerilir ve sn, SEC 17A-4 (f) ve diğer mevzuata uyumluluğuyla uyumlu olması için kilitlenmelidir. Zaman tabanlı bekletme ilkesi kilitlendiğinde, ilke kaldırılamaz ve etkin saklama süresine en fazla 5 artış yapılabilir. Zamana dayalı saklama ilkelerini ayarlama ve kilitleme hakkında daha fazla bilgi için [Başlarken](#getting-started) bölümüne bakın.

### <a name="legal-holds"></a>Yasal tutma

Yasal bir saklama ayarladığınızda, yasal saklama temizlenene kadar tüm mevcut ve yeni Bloblar sabit durumda kalır. Yasal tutmaları ayarlama ve temizleme hakkında daha fazla bilgi için [Başlarken](#getting-started) bölümüne bakın.

Bir kapsayıcı aynı anda hem yasal ayrı tutmaya hem de zaman tabanlı bir bekletme ilkesine sahip olabilir. Geçerli saklama süresi sona erse bile, bu kapsayıcıdaki tüm Bloblar, tüm yasal tutmalar temizlenene kadar sabit durumda kalır. Buna karşılık, bir blob, yasal saklama süresi sona erene kadar sabit bir durumda kalır, ancak tüm yasal tutmalar temizlenmiş olsa da geçerlidir.

Aşağıdaki tabloda, farklı sabit senaryolar için devre dışı bırakılmış blob işlemlerinin türleri gösterilmektedir. Daha fazla bilgi için bkz. [Azure Blob hizmeti API 'si](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) belgeleri.

|Senaryo  |Blob durumu  |Blob işlemlerine izin verilmiyor  |
|---------|---------|---------|
|Blobdaki geçerli saklama süresi dolmadı ve/veya yasal tutma ayarlandı     |Sabit: hem silme hem de yazma korumalı         | Blob<sup>1</sup>koyma, yerleştirme blok<sup>1</sup>, yerleştirme listesi<sup>1</sup>, kapsayıcıyı silme, blobu silme, blob meta verilerini ayarlama, yerleştirme sayfası, blob özelliklerini ayarlama, anlık görüntü blobu, artımlı kopyalama blobu, ekleme bloğu         |
|Blob üzerindeki geçerli saklama süresi doldu     |Yalnızca yazma korumalı (silme işlemlerine izin verilir)         |Blob<sup>1</sup>, put bloğu<sup>1</sup>, yerleştirme, blok listesi<sup>1</sup>, blob meta verilerini ayarlama, yerleştirme sayfası, blob özelliklerini ayarlama, anlık görüntü blobu, artımlı kopyalama blobu, ekleme bloğu         |
|Tüm yasal tutar temizlendi ve kapsayıcıda zaman tabanlı bekletme ilkesi ayarlanmadı     |Değiştirilebilir         |None         |
|Bir solucan ilkesi oluşturulmaz (zamana dayalı saklama veya yasal saklama)     |Değiştirilebilir         |None         |

<sup>1</sup> uygulama bu işlemlerin bir kez yeni blob oluşturmasına izin verir. Sabit bir kapsayıcıda var olan bir blob yolundaki tüm sonraki üzerine yazma işlemlerine izin verilmez.

## <a name="supported-values"></a>Desteklenen değerler

### <a name="time-based-retention"></a>Zamana dayalı saklama
- Depolama hesabı için, kilitli zamana dayalı sabit ilkelerle maksimum kapsayıcı sayısı 1.000 ' dir.
- En düşük saklama aralığı 1 gündür. En fazla 146.000 gün (400 yıl).
- Bir kapsayıcı için, kilitli zaman tabanlı sabit ilkeler için bir bekletme aralığını uzatmak üzere en fazla düzenleme sayısı 5 ' tir.
- Bir kapsayıcı için, kilitli bir ilke için en fazla 7 zaman tabanlı bekletme ilkesi denetim günlüğü tutulur.

### <a name="legal-hold"></a>Yasal tutma
- Bir depolama hesabı için, geçerli bir saklama ayarı olan en fazla kapsayıcı sayısı 1.000 ' dir.
- Bir kapsayıcı için, en fazla yasal saklama etiketi sayısı 10 ' dur.
- Geçerli bir saklama etiketinin en az uzunluğu 3 alfasayısal karakterdir. Maksimum uzunluk 23 alfasayısal karakterdir.
- Bir kapsayıcı için, ilke süresince en fazla 10 yasal saklama ilkesi denetim günlüğü tutulur.

## <a name="pricing"></a>Fiyatlandırma

Bu özelliği kullanmak için ek ücret alınmaz. Değişmez veriler normal, değişebilir verilerle aynı şekilde fiyatlandırılır. Azure Blob depolama hakkındaki fiyatlandırma ayrıntıları için bkz. [Azure Depolama fiyatlandırması sayfası](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="getting-started"></a>Başlarken
Sabit depolama yalnızca Genel Amaçlı v2 ve BLOB depolama hesapları için kullanılabilir. Bu hesapların [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)ile yönetilmesi gerekir. Mevcut Genel Amaçlı v1 depolama hesabını yükseltme hakkında daha fazla bilgi için bkz. [Depolama hesabını yükseltme](../common/storage-account-upgrade.md).

[Azure Portal](https://portal.azure.com), [azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)ve [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) en son sürümleri, Azure Blob depolama için sabit depolamayı destekler. [İstemci kitaplığı desteği](#client-libraries) de sağlanır.

### <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Sabit durumda tutulması gereken blobların depolanması için yeni bir kapsayıcı oluşturun veya mevcut bir kapsayıcıyı seçin.
 Kapsayıcının bir GPv2 veya blob depolama hesabında olması gerekir.
2. Kapsayıcı ayarları ' nda **erişim ilkesi** ' ni seçin. Ardından, **sabit blob depolaması**altında **+ ilke Ekle** ' yi seçin.

    ![Portalda kapsayıcı ayarları](media/storage-blob-immutable-storage/portal-image-1.png)

3. Zamana dayalı saklama süresini etkinleştirmek için, açılan menüden **zamana dayalı saklama** ' yı seçin.

    !["Ilke türü" altında "zaman tabanlı bekletme" seçildi](media/storage-blob-immutable-storage/portal-image-2.png)

4. Bekletme aralığını gün cinsinden girin (kabul edilebilir 1 ile 146000 gün).

    !["Saklama süresini güncelleştirme" kutusu](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    İlkenin ilk durumu kilidi açmadan önce özelliği test etmeniz ve ilkede değişiklikler yapmanız sağlanır. İlkeyi kilitlemek, SEC 17A-4 gibi yönetmeliklerle uyumluluk açısından önemlidir.

5. İlkeyi kilitleyin. Üç nokta ( **...** ) simgesine sağ tıklayın ve aşağıdaki menü ek eylemlerle görüntülenir:

    ![Menüdeki "ilkeyi kilitle"](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. **Kilit ilkesini** seçin ve kilidi onaylayın. İlke artık kilitli ve silinemez, yalnızca bekletme aralığının uzantılarına izin verilir. Blob silme ve geçersiz kılmalara izin verilmez. 

    ![Menüde "kilit ilkesini" onaylayın](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. Yasal tutmaları etkinleştirmek için **+ Ilke Ekle**' yi seçin. Açılan menüden **yasal tut** ' u seçin.

    !["Ilke türü" altındaki menüdeki "Legal Hold"](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. Bir veya daha fazla etiketle geçerli bir saklama alanı oluşturun.

    ![İlke türü altındaki "etiket adı" kutusu](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. Yasal bir tutmayı temizlemek için uygulanan geçerli saklama tanımlayıcı etiketini kaldırmanız yeterlidir.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu özellik aşağıdaki komut gruplarına dahildir: `az storage container immutability-policy` ve `az storage container legal-hold`. Komutları görmek için `-h` çalıştırın.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Az. Storage modülü, sabit depolamayı destekler.  Özelliği etkinleştirmek için şu adımları izleyin:

1. PowerShellGet 'in en son sürümünün yüklü olduğundan emin olun: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Önceki Azure PowerShell yüklemesini kaldırın.
3. Azure PowerShell yüklensin: `Install-Module Az –Repository PSGallery –AllowClobber`.

[Örnek PowerShell kodu](#sample-powershell-code) bölümü bu makalede daha sonra özellik kullanımı gösterilmektedir.

---

## <a name="client-libraries"></a>İstemci kitaplıkları

Aşağıdaki istemci kitaplıkları Azure Blob depolama için sabit depolamayı destekler:

- [.NET Istemci kitaplığı sürüm 7.2.0-Önizleme ve üzeri](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Node. js Istemci kitaplığı sürüm 4.0.0 ve üzeri](https://www.npmjs.com/package/azure-arm-storage)
- [Python Istemci kitaplığı sürüm 2.0.0 sürüm adayı 2 ve üzeri](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Java Istemci kitaplığı](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="faq"></a>SSS

**SOLUCAN uyumluluğuna ilişkin belgeler verebilir misiniz?**

Evet. Microsoft, uyumluluğu belgelemek için kayıt yönetimi ve bilgi İdaresi, Cohasset Associates ' de uzmanlaşmış, Azure sabit BLOB depolama alanını ve gereksinimlere özgü uyumluluğu sağlamak için, önde gelen bağımsız bir değerlendirme firması elde eder Finansal Hizmetler sektörüne. Cohasset, Azure sabit blob depolaması 'nın, bir solucan durumunda zaman tabanlı Blobları sürdürmek için kullanıldığında, CFTC Rule 1.31 (c)-(d), FINRA kuralı 4511 ve sn kuralı 17A-4 ' nin ilgili depolama gereksinimlerini karşıladığını doğruladı. Microsoft, bu kural kümesini hedefleyen, mali kurumlara yönelik kayıtlar için küresel olarak en iyi şekilde koruma kılavuzunu temsil etmektedir. Cohasset raporu, [Microsoft hizmet Güven Merkezi](https://aka.ms/AzureWormStorage)' nde kullanılabilir. Microsoft 'tan solucan uyumluluğuna ilişkin bir kanıtlama mektubu istemek için lütfen Azure desteğine başvurun.

**Özellik yalnızca blob 'ları engellemek veya sayfa ve ekleme Blobları da için geçerlidir mi?**

Sabit depolama, kapsayıcı düzeyinde ayarlandığı için herhangi bir blob türüyle kullanılabilir, ancak genellikle blok bloblarını depolayan kapsayıcılar için solucan kullanmanızı öneririz. Blok Bloblarından farklı olarak, tüm yeni sayfa Blobları ve ekleme Blobları bir solucan kapsayıcısının dışında oluşturulmalıdır ve sonra içine kopyalanmalıdır. Bu Blobları bir solucan kapsayıcısına kopyaladıktan sonra, bir ekleme blobuna *başka ekleme* veya sayfa blobuna yapılan değişikliklere izin verilmez. Bu nedenle, sanal makine diskini kilitleyecek şekilde VHD 'leri (sayfa Blobları) depolayan bir kapsayıcıda bir solucan ilkesi ayarlamak kesinlikle önerilmez.

**Bu özelliği kullanmak için yeni bir depolama hesabı oluşturmem gerekir mi?**

Hayır, mevcut veya yeni oluşturulan Genel Amaçlı v2 veya blob depolama hesaplarıyla sabit depolama kullanabilirsiniz. Bu özellik, GPv2 ve BLOB depolama hesaplarında blok Blobları ile kullanım için tasarlanmıştır. Genel Amaçlı v1 depolama hesapları desteklenmez, ancak Genel Amaçlı v2 'ye kolayca yükseltilebilir. Mevcut Genel Amaçlı v1 depolama hesabını yükseltme hakkında daha fazla bilgi için bkz. [Depolama hesabını yükseltme](../common/storage-account-upgrade.md).

**Yalnızca yasal bir saklama ve zaman tabanlı bekletme ilkesi uygulayabilir miyim?**

Evet, bir kapsayıcıda aynı anda hem yasal bir saklama hem de zaman tabanlı bir bekletme ilkesi bulunabilir. Geçerli saklama süresi sona erse bile, bu kapsayıcıdaki tüm Bloblar, tüm yasal tutmalar temizlenene kadar sabit durumda kalır. Buna karşılık, bir blob, yasal saklama süresi sona erene kadar sabit bir durumda kalır, ancak tüm yasal tutmalar temizlenmiş olsa da geçerlidir.

**Yasal saklama ilkeleri yalnızca yasal bir şekilde geçerlidir veya başka kullanım senaryolarında bulunabilir mi?**

Hayır, yasal saklama yalnızca zamana bağlı olmayan bir bekletme ilkesi için kullanılan genel bir terimdir. Yalnızca bu konuyla ilgili yaklaşım için kullanılması gerekmez. Yasal saklama ilkeleri, bekletme döneminin bilinmediği önemli kurumsal solucan verilerini korumak için üzerine yazma ve silmeleri devre dışı bırakmak için yararlıdır. Görev açısından kritik solucan iş yüklerinizi korumak için bir kuruluş ilkesi olarak kullanabilir veya özel bir olay tetikleyicisi zaman tabanlı bir bekletme ilkesi kullanımını gerektirdiğinden bunu bir hazırlama ilkesi olarak kullanabilirsiniz. 

**_Kilitli_ bir zaman tabanlı bekletme ilkesi veya yasal tutmayı kaldırabilir miyim?**

Yalnızca kilitlenmemiş süre tabanlı bekletme ilkeleri bir kapsayıcıdan kaldırılabilir. Zaman tabanlı bekletme ilkesi kilitlendikten sonra kaldırılamaz; Yalnızca etkili Bekletme dönemi uzantılarına izin verilir. Yasal saklama etiketleri silinebilir. Tüm yasal Etiketler silindiğinde, yasal saklama kaldırılır.

**Zaman tabanlı saklama veya yasal tutma ilkesi ile *kilitlenmiş* bir kapsayıcıyı silmeye çalışırsam ne olur?**

Kilitli zaman tabanlı bekletme ilkesine veya yasal tutmaya sahip en az bir blob varsa, kapsayıcıyı silme işlemi başarısız olur. Kapsayıcıyı silme işlemi yalnızca etkin saklama aralığı olan bir blob yoksa ve geçerli tutar yoksa başarılı olur. Kapsayıcıyı silebilmek için Blobları silmeniz gerekir.

**Zaman tabanlı saklama veya yasal tutma ilkesi ile *kilitlenmiş* bir WORM kapsayıcısı bulunan depolama hesabını silmeye çalışırsam ne olur?**

Yasal tutma ilkesine veya zaman tabanlı saklama aralığına sahip en az bir WORM kapsayıcısı olması halinde depolama hesabı silme işlemi başarısız olur. Depolama hesabını silebilmeniz için önce tüm solucan kapsayıcılarını silmeniz gerekir. Kapsayıcı silme hakkında daha fazla bilgi için önceki soruya bakın.

**Blob sabit durumda olduğunda farklı blob katmanları (sık erişimli, seyrek erişimli, soğuk depolama) arasında veri aktarımı gerçekleştirebilir miyim?**

Evet, verileri uyumlu sabit durumda tutarken blob katmanlarında verileri taşımak için blob katmanını ayarla komutunu kullanabilirsiniz. Sabit depolama, sık erişimli, seyrek erişimli ve arşiv blob katmanlarında desteklenir.

**Ödemeyi yapamadıysam ve saklama süresi dolmadıysa ne olur?**

Ödeme dışı durumda, normal veri saklama ilkeleri Microsoft ile sözleşmenizin hüküm ve koşullarından dolayı geçerli olur.

**Özelliği deneyebilmek için deneme veya yetkisiz kullanım süresi sunuyor musunuz?**

Evet. Zaman tabanlı bir bekletme ilkesi ilk oluşturulduğunda, *kilidi açılmış* durumda olur. Bu durumda, bekletme aralığında (artış veya azaltma, hatta ilkeyi silme gibi) istediğiniz değişikliği yapabilirsiniz. İlke kilitlendikten sonra, saklama aralığı sona erene kadar kilitli kalır. Bu kilitli ilke, saklama aralığına silme ve değiştirme yapılmasını engeller. *Kilidi açık* durumdan yalnızca deneme amacıyla kullanmanızı ve ilkeyi 24 saatlik bir süre içinde kilitlemeyi önemle öneririz. Bu uygulamalar, SEC 17A-4 (f) ve diğer yönetmeliklere uyum sağlamanıza yardımcı olur.

**Sabit blob ilkeleriyle birlikte geçici silme kullanabilir miyim?**

Evet. [Azure Blob depolama Için geçici silme](storage-blob-soft-delete.md) , yasal bir saklama veya zaman tabanlı bekletme ilkesinden bağımsız olarak bir depolama hesabındaki tüm kapsayıcılar için geçerlidir. Herhangi bir sabit solucan İlkesi uygulanmadan ve onaylanmadan önce ek koruma için geçici silme özelliğinin etkinleştirilmesini öneririz. 

**Özellik nerede kullanılabilir?**

Sabit depolama, Azure genel, Çin ve kamu bölgelerinde kullanılabilir. Bölgenizde sabit depolama alanı yoksa, lütfen desteğe ve e-posta azurestoragefeedback@microsoft.combaşvurun.

## <a name="sample-powershell-code"></a>Örnek PowerShell kodu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aşağıdaki örnek PowerShell betiği başvuru içindir. Bu betik yeni bir depolama hesabı ve kapsayıcısı oluşturur. Daha sonra yasal tutmaları ayarlama ve Temizleme, zaman tabanlı bir bekletme ilkesi oluşturma ve kilitleme (aynı zamanda, imlebilirlik ilkesi olarak da bilinir) ve bekletme aralığını genişletme işlemlerinin nasıl yapılacağını gösterir.

Azure Depolama hesabını ayarlama ve test etme:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
$account = New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container using the context
New-AzStorageContainer -Name $container -Context $account.Context

# Get a container
$container = Get-AzStorageContainer -Name $container -Context $account.Context

# List containers
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Yasal tutmaları ayarla ve temizle:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

İmlebilirlik kuralları oluşturma veya güncelleştirme:
```powershell
# with an account name or container name
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

İmlebilirlik kuralları alma:
```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzRmStorageContainerImmutabilityPolicy -Container $containerObject
```

Dengesizin kullanılabilirliği ilkelerini kilitle (istemi kapatmak için Add-zorlama):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Dengesde kullanılabilirlik ilkelerini uzat:
```powershell

# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Kilitsiz bir Indirgenme ilkesini kaldırma (istemi kapatmak için Add-zorlama):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
