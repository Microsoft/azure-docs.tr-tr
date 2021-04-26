---
title: Azure Blob depolama için erişim katmanları-sık erişimli, seyrek erişimli ve arşiv
description: Azure Blob depolama için sık erişimli, seyrek erişimli ve arşiv erişim katmanları hakkında bilgi edinin. Katmanlamayı destekleyen depolama hesaplarını gözden geçirin.
author: twooley
ms.author: twooley
ms.date: 03/18/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: klaasl
ms.openlocfilehash: 957973cc4f53dba10ed9d635c8e3f69fd66ee33b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278431"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Azure Blob depolama için erişim katmanları-sık erişimli, seyrek erişimli ve arşiv

Azure depolama, farklı erişim katmanları sunarak blob nesne verilerini en uygun maliyetli biçimde depolamanıza olanak tanır. Kullanılabilir erişim katmanları şunları içerir:

- Sık erişilen verileri depolamak **için sık iyileştirilmiş** .
-  Seyrek erişimli ve en az 30 gün boyunca depolanan verileri depolamak için en iyi duruma getirilmiştir.
- **Arşiv** -Seyrek erişilen ve en az 180 gün boyunca depolanan ve saat sırasına göre esnek gecikme süresi gereksinimleriyle saklanan verileri depolamak için optimize edilmiştir.

Farklı erişim katmanlarıyla ilgili olarak dikkat edilmesi gereken noktalar şunlardır:

- Erişim katmanı karşıya yükleme sırasında veya sonrasında bir blob üzerinde ayarlanabilir.
- Sadece sık erişim ve seyrek erişim katmanları hesap düzeyinde ayarlanabilir. Arşiv erişim katmanı yalnızca blob düzeyinde ayarlanabilir.
- Seyrek Erişimli katmandaki verilerin kullanılabilirliği biraz daha düşüktür, ancak hala yüksek dayanıklılık, alma gecikmesi ve dinamik verilere benzer işleme özellikleri vardır. Seyrek Erişimli veriler için, hafif düzeyde düşük kullanılabilirlik ve daha yüksek erişim maliyetleri, sık erişimli verilerle karşılaştırıldığında düşük genel depolama maliyetleri için kabul edilebilir. Daha fazla bilgi için bkz. [depolama SLA’sı](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).
- Arşiv erişim katmanındaki veriler çevrimdışı olarak depolanır. Arşiv katmanı en düşük depolama maliyetlerini ve ayrıca en yüksek erişim maliyetlerini ve gecikmesini sunar.
- Sık ve seyrek erişimli katmanlar tüm artıklık seçeneklerini destekler. Arşiv katmanı yalnızca LRS, GRS ve RA-GRS 'yi destekler.
- Veri depolama sınırları, erişim katmanına göre değil, hesap düzeyinde ayarlanır. Tüm sınırınızı tek bir katmanda veya üç katmanın tamamında kullanmayı seçebilirsiniz.

Bulutta depolanan veriler üstel bir hızda artar. Artan depolama ihtiyaçlarınızın maliyetlerini yönetmek için, maliyetleri optimize etmek amacıyla erişim sıklığı ve planlanan elde tutma dönemi gibi özniteliklere bağlı olarak verilerinizi düzenlemek yararlıdır. Bulutta depolanan verilerin oluşturma, işleme ve ömrü boyunca erişim özellikleri farklı olabilir. Bazı veriler ve yaşam süresi boyunca aktif şekilde erişilebilir ve değiştirilebilir. Bazı verilere, veriler eskidikçe önemli ölçüde azalan erişimle, yaşam sürelerinin başlarında sık erişilebilir. Bulutta depolanan bazı veriler boşta kalır ve depolandıktan sonra çok nadir erişim sağlanır.

Bu veri erişim senaryolarının her biri, belirli bir erişim deseninin en iyi duruma getirilmiş farklı bir erişim katmanından faydalanır. Sık erişimli, seyrek erişimli ve arşiv erişim katmanlarında Azure Blob depolama, ayrı fiyatlandırma modelleriyle farklı erişim katmanları için bu gereksinimi ortadan gideriyor.

Aşağıdaki araçlar ve istemci kitaplıkları, blob düzeyi katmanlama ve arşiv depolamayı destekler.

- Azure portal
- PowerShell
- Azure CLı araçları
- .NET istemci kitaplığı
- Java istemci kitaplığı
- Python istemci kitaplığı
- Node.js istemci kitaplığı

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Katman ayarlamayı destekleyen depolama hesapları

Sık erişimli, seyrek erişimli ve arşiv arasında nesne depolama verileri katmanlama, BLOB depolama ve Genel Amaçlı v2 (GPv2) hesaplarında desteklenir. Genel Amaçlı v1 (GPv1) hesapları katmanlamayı desteklemez. Mevcut GPv1 veya blob depolama hesaplarınızı Azure portal aracılığıyla GPv2 hesaplarına kolayca dönüştürebilirsiniz. GPv2; Bloblar, dosyalar ve kuyruklar için yeni fiyatlandırma ve özellikler sağlar. Bazı özellikler ve fiyat keser yalnızca GPv2 hesaplarında sunulur. Bazı iş yükleri GPv1 'ten GPv2 üzerinde daha pahalı olabilir. Daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](../common/storage-account-overview.md).

BLOB depolama ve GPv2 hesapları, **erişim katmanı** özniteliğini hesap düzeyinde kullanıma sunar. Bu öznitelik, nesne düzeyinde açıkça ayarlanmamış olan herhangi bir blob için varsayılan erişim katmanını belirtmenize olanak tanır. Katmanı açıkça ayarlanmış nesneler için hesap katmanı uygulanmaz. Arşiv katmanı yalnızca nesne düzeyinde uygulanabilir. İstediğiniz zaman erişim katmanları arasında geçiş yapabilirsiniz.

Katmanlama için BLOB depolama hesapları yerine GPv2 kullanın. GPv2, BLOB depolama hesaplarının desteklediği tüm özellikleri ve çok daha fazlasını destekler. BLOB depolama ve GPv2 arasındaki fiyatlandırma neredeyse aynıdır, ancak bazı yeni özellikler ve fiyat keser yalnızca GPv2 hesaplarında kullanılabilir.

GPv1 ve GPv2 hesapları arasındaki fiyat yapısı farklıdır ve müşteriler GPv2 hesaplarını kullanmaya karar vermeden önce her ikisini de dikkatle değerlendirmelidir. Var olan bir Blob Depolama veya GPv1 hesabını tek tıklamada basit bir işlemle kolayca GPv2’ye dönüştürebilirsiniz. Daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](../common/storage-account-overview.md).

## <a name="hot-access-tier"></a>Sık erişim katmanı

Sık erişim katmanının depolama maliyetleri seyrek ve arşiv katmanlarına kıyasla daha yüksektir ancak erişim maliyetleri en düşük olan seçenek budur. Sık erişim katmanı için örnek kullanım senaryolarından bazıları şunlardır:

- Etkin kullanımda olan veya sık olarak yazılması beklenen veriler
- Seyrek erişim katmanına işleme ve nihai geçiş için hazırlanan veriler

## <a name="cool-access-tier"></a>Seyrek erişim katmanı

Seyrek erişim katmanı, sık erişim katmanına kıyasla daha düşük depolama maliyetine ve daha yüksek erişim maliyetine sahiptir. Bu katman, seyrek erişim katmanında en az 30 gün boyunca kalacak verilere yöneliktir. Seyrek erişim katmanı için örnek kullanım senaryolarından bazıları şunlardır:

- Kısa vadeli yedekleme ve olağanüstü durum kurtarma
- Eski veriler sık kullanılmıyor ancak erişildiğinde hemen kullanılabilir olması bekleniyor
- Daha sonra işlenmek üzere daha fazla veri toplanırken, maliyeti etkili bir şekilde depolanması gereken büyük veri kümeleri

## <a name="archive-access-tier"></a>Arşiv erişim katmanı

Arşiv erişim katmanının en düşük depolama maliyeti, ancak sık erişimli ve seyrek katmanlarla karşılaştırıldığında daha yüksek veri alma maliyetlerine sahiptir. Verilerin arşiv katmanında en az 180 gün kalması veya erken bir silme ücretine tabi olması gerekir. Arşiv katmanındaki verilerin, belirtilen yeniden doldurma önceliğine bağlı olarak, alınması birkaç saat sürebilir. Küçük nesneler için, yüksek öncelikli bir yeniden doldurma, nesneyi bir saat içinde arşivden alabilir. Daha fazla bilgi edinmek için [Arşiv katmanından blob verilerini yeniden doldurma](storage-blob-rehydration.md) bölümüne bakın.

Blob arşiv depolamadaki sırada blob verileri çevrimdışı olduğundan okunamaz veya değiştirilemez. Arşivdeki bir blobu okumak veya indirmek için önce çevrimiçi bir katmana yeniden yazmanız gerekir. Arşiv depolamadaki bir Blobun anlık görüntülerini alamazsınız. Ancak, blob meta verileri çevrimiçi ve kullanılabilir durumda kalır, bu da blobu, özelliklerini, meta verilerini ve BLOB Dizin etiketlerini listelemenize olanak sağlar. Arşiv sırasında blob meta verilerini ayarlamaya veya değiştirmeye izin verilmiyor. Ancak, blob dizini etiketlerini ayarlayabilir ve değiştirebilirsiniz. Arşivdeki Bloblar için, yalnızca [BLOB özelliklerini al](/rest/api/storageservices/get-blob-properties), [BLOB meta verilerini al](/rest/api/storageservices/get-blob-metadata), blob [etiketlerini ayarla](/rest/api/storageservices/set-blob-tags), [BLOB etiketlerini al](/rest/api/storageservices/get-blob-tags), [etiketlere göre blob 'ları bul](/rest/api/storageservices/find-blobs-by-tags), blobu [Listele](/rest/api/storageservices/list-blobs), blob [katmanını ayarla](/rest/api/storageservices/set-blob-tier), blobu [Kopyala](/rest/api/storageservices/copy-blob)ve [blobu Sil](/rest/api/storageservices/delete-blob).

Arşiv erişim katmanı için örnek kullanım senaryoları şunları içerir:

- Uzun vadeli yedekleme, ikincil yedekleme ve arşiv veri kümeleri
- Son kullanılabilir biçime işlendikten sonra bile korunması gereken özgün (ham) veriler
- Uzun bir süre içinde depolanması gereken uyumluluk ve arşiv verileri

> [!NOTE]
> ZRS, GZRS veya RA-GZRS hesapları için Arşiv katmanı desteklenmez. LRS 'den GRS 'ye geçiş, hesap LRS olarak ayarlandığı sürece arşiv katmanına taşınamadığından blob 'lar için desteklenir. Güncelleştirme, hesap LRS 'den önce 30 günden az olursa ve hesap LRS olarak ayarlandığında arşiv katmanına taşınamadığından bir hesap geri taşınabilir.

## <a name="account-level-tiering"></a>Hesap düzeyi katmanlama

Her üç erişim katmanında blob 'lar aynı hesap içinde bulunabilir. Açıkça atanan bir katmana sahip olmayan herhangi bir blob, katmanı hesap erişim katmanı ayarından algılar. Erişim katmanı hesaptan geliyorsa, erişim katmanı blobu **çıkarılan** blob özelliği "true" olarak ayarlanmış ve **erişim katmanı** blobu özelliği hesap katmanıyla eşleşiyor demektir. Azure portal, _erişim katmanının çıkartılan_ özelliği, blob erişim katmanı ile **sık erişimli (çıkarılabilen)** veya seyrek erişimli **(çıkarılan)** olarak görüntülenir.

Hesap erişim katmanını değiştirmek, bir açık katman kümesi olmayan hesapta depolanan tüm _erişim katmanı_ nesneleri için geçerlidir. Hesap katmanını sık erişimli 'den seyrek erişimli olarak değiştirirseniz, yalnızca GPv2 hesaplarında ayarlanmış bir katman olmadan tüm Bloblar için yazma işlemleri (10.000 başına) için ücretlendirilirsiniz. BLOB depolama hesaplarında bu değişiklik için ücret alınmaz. Seyrek Erişimli BLOB depolama veya GPv2 hesaplarında sık erişimli olarak geçiş yaparsanız, hem okuma işlemleri (10.000 başına) hem de veri alımı (GB başına) için ücretlendirilirsiniz.

Yalnızca sık ve seyrek erişimli katmanlar varsayılan hesap erişim katmanı olarak ayarlanabilir. Arşiv yalnızca nesne düzeyinde ayarlanabilir. Blob yükleme sırasında, tercih ettiğiniz erişim katmanını, varsayılan hesap katmanından bağımsız olarak sık erişimli, seyrek erişimli veya arşiv olacak şekilde belirtebilirsiniz. Bu işlevsellik, blob depolamada veri oluşturduğunuz andan itibaren maliyet tasarrufu sağlamak için doğrudan arşiv katmanına veri yazmanızı sağlar.

## <a name="blob-level-tiering"></a>Blob düzeyinde katman ayarlama

Blob düzeyi katmanlama [, blob](/rest/api/storageservices/put-blob) katmanı veya [PUT blok listesi](/rest/api/storageservices/put-block-list) işlemlerini kullanarak seçtiğiniz erişim katmanına veri yüklemenize ve [BLOB katmanını ayarla](/rest/api/storageservices/set-blob-tier) işlemini veya [yaşam döngüsü yönetimi](#blob-lifecycle-management) özelliğini kullanarak nesne düzeyinde verilerinizin katmanını değiştirmenize olanak sağlar. Verileri, gerekli erişim katmanına yükleyebilir ve kullanım desenleri değiştikçe, verileri hesaplar arasında taşımaya gerek kalmadan, sık erişimli, seyrek erişimli veya arşiv katmanları arasında blob erişim katmanını kolayca değiştirebilirsiniz. Tüm katman değişikliği istekleri hemen gerçekleşir ve sık erişimli ve Seyrek Erişimli Katman değişiklikleri anında gerçekleşir. Arşiv katmanından bir blobu yeniden doldurma birkaç saat sürebilir.

Son blob katmanı değişikliğinin zamanı, **Erişim Katmanı Değişim Zamanı** blob özelliği aracılığıyla gösterilir. **Erişim katmanı değiştirme zamanı** blob düzeyi bir özelliktir ve varsayılan hesap katmanı değiştirildiğinde güncellenmez. Hesap özellikleri ve BLOB özellikleri ayrı. Hesabın varsayılan erişim katmanı değiştirildiğinde, bir depolama hesabındaki her Blobun **erişim katmanı değişiklik süresini** güncelleştirmek mümkün maliyetli hale gelir.

Sık erişimli veya seyrek erişimli katmanda bir Blobun üzerine yazarken, yeni oluşturulan blob, yeni blob erişim katmanı oluşturma sırasında açıkça ayarlanmadığı takdirde, üzerine yazılan Blobun katmanını devralır. Blob arşiv katmanındaysa üzerine yazılamaz, bu nedenle aynı Blobun karşıya yüklemeye Bu senaryoda izin verilmez.

> [!NOTE]
> Arşiv depolama ve blob düzeyinde katman ayarlama, yalnızca blok bloblarını destekler.

### <a name="blob-lifecycle-management"></a>Blob yaşam döngüsü yönetimi

Blob Storage yaşam döngüsü yönetimi, verilerinizi en iyi erişim katmanına geçirmeye ve yaşam döngüsünün sonunda verilerin süresini dolabilmeniz için kullanabileceğiniz zengin, kural tabanlı bir ilke sunar. Daha fazla bilgi edinmek için bkz. [Azure Blob depolama erişim katmanlarını otomatikleştirerek maliyetleri iyileştirme](storage-lifecycle-management-concepts.md) .

> [!NOTE]
> Blok Blobu depolama hesabında (Premium performans) depolanan veriler şu anda [BLOB katmanını ayarla](/rest/api/storageservices/set-blob-tier) veya Azure Blob depolama yaşam döngüsü yönetimi kullanılarak sık erişimli, seyrek erişimli veya arşiv olarak katmanlanamaz.
> Verileri taşımak için, Blok Blob depolama hesabındaki Blobları, [URL API 'Sinden put bloğu](/rest/api/storageservices/put-block-from-url) veya bu API 'yi destekleyen bir AzCopy sürümü kullanarak farklı bir hesapta bulunan sık erişimli erişim katmanına kopyalamanız gerekir.
> **URL API 'Den yerleştirme bloğu** , sunucudaki verileri eşzamanlı olarak kopyalar, yani çağrı yalnızca tüm veriler özgün sunucu konumundan hedef konuma taşındıktan sonra tamamlanır.

### <a name="blob-level-tiering-billing"></a>Blob düzeyinde katman ayarlama faturalandırması

Bir blob karşıya yüklendiğinde veya katmanlar arasında taşındığında, bu, yükleme veya katman değişikliğine anında karşılık gelen fiyata göre ücretlendirilir.

Bir blob daha soğuk bir katmana taşındığında (sık erişimli >seyrek erişimli, >sık erişimli arşiv veya seyrek erişimli >Arşivi), bu işlem hedef katmana yazma işlemi olarak faturalandırılır ve hedef katmanın yazma işlemi (10.000 başına) ile veri yazma (GB başına) ücretleri uygulanır.

Bir blob bir çarpıtma katmanına taşındığında (Arşiv->seyrek erişimli, arşiv >sık erişimli >veya seyrek erişimli), bu işlem kaynak katmandan okuma olarak faturalandırılır ve kaynak katmanın okuma işlemi (10.000 başına) ile veri alma (GB başına) ücretleri uygulanır. Seyrek Erişimli veya arşiv katmanının dışına taşınan herhangi bir blob için [erken silme](#cool-and-archive-early-deletion) ücretleri de uygulanabilir. [Arşiv katmanından verileri yeniden doldurma](storage-blob-rehydration.md) işlemi zaman alır ve veriler çevrimiçi olarak geri yüklenene ve BLOB katmanı sık erişimli veya seyrek erişimli olarak değişene kadar arşiv fiyatları üzerinden ücretlendirilir.

Aşağıdaki tabloda, katman değişikliklerinin nasıl faturalandırıldığını özetlenmektedir.

| | **Yazma ücretleri (işlem + erişim)** | **Okuma ücretleri (işlem + erişim)** |
| ---- | ----- | ----- |
| **Blob Katmanını Ayarla** | sık erişimli > Seyrek Erişimli<br> sık erişimli > Arşivi<br> Seyrek Erişimli > Arşivi | Arşiv-> seyrek<br> Arşiv-> sık erişimli<br> Seyrek Erişimli > sık erişimli

### <a name="cool-and-archive-early-deletion"></a>Seyrek erişimli ve arşiv erken silme

Seyrek Erişimli katmana taşınan herhangi bir blob (yalnızca GPv2 hesapları), sık erişimli 30 günlük bir erken silme süresine tabidir. Arşiv katmanına taşınan herhangi bir blob, arşiv erken silme dönemi olan 180 gün ile tabidir. Bu ücret eşit olarak bölünür. Örneğin, bir blob arşive taşınır ve 45 gün sonra sık erişimli katmana taşınırsa veya taşınmışsa, bu Blobun arşiv 'de depolanması için bir erken silme ücreti, 135 (180 eksi 45) güne denk ücretlendirilirsiniz.

Seyrek erişimli ve arşiv katmanları arasında hareket eden bazı ayrıntılar:

1. Blob, depolama hesabının varsayılan erişim katmanına göre seyrek erişimli olarak çıkarılırsa ve BLOB arşive taşınırsa, erken silme ücreti yoktur.
1. Bir blob açık olarak seyrek erişimli katmana taşınırsa ve sonra arşive taşınırsa, erken silme ücreti uygulanır.

Bir erişim katmanı değişikliği yoksa, **son değiştirilen** blob özelliğini kullanarak erken silme süresini hesaplayın. Aksi takdirde, erişim katmanının en son seyrek erişimli veya arşiv olarak değiştirildiği durumlarda kullanın: **erişim katmanı değiştirme zamanı**. Blob özellikleri hakkında daha fazla bilgi için bkz. [BLOB özelliklerini al](/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Blok Blobu depolama seçeneklerini karşılaştırma

Aşağıdaki tabloda, Premium performans bloğu blob depolaması ve sık erişimli, seyrek erişimli ve arşiv erişim katmanları karşılaştırması gösterilmektedir.

|                                           | **Premium performans**   | **Etkin katman** | **Cool katmanı**       | **Arşiv katmanı**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Kullanılabilirlik**                          | %99,9                     | %99,9        | %99                 | Çevrimdışı           |
| **Kullanılabilirlik** <br> **(RA-GRS okumaları)**  | Yok                       | %99,99       | %99,9               | Çevrimdışı           |
| **Kullanım ücretleri**                         | Daha yüksek depolama maliyetleri, daha düşük erişim ve işlem maliyeti | Daha yüksek depolama maliyetleri, daha düşük erişim ve işlem maliyetleri | Daha düşük depolama maliyetleri, daha yüksek erişim ve işlem maliyetleri | En düşük depolama maliyetleri, en yüksek erişim ve işlem maliyetleri |
| **En az depolama süresi**              | Yok                       | Yok          | 30 gün<sup>1</sup> | 180 gün
| **Gecikme süresi** <br> **(İlk bayta kalan süre)** | Tek basamaklı milisaniye | milisaniye | milisaniye        | Saat<sup>2</sup> |

GPv2 hesaplarındaki seyrek katmandaki <sup>1</sup> nesne en az 30 günlük saklama süresine sahiptir. BLOB depolama hesapları, seyrek erişimli katman için en düşük saklama süresine sahip değildir.

<sup>2</sup> arşiv depolama Şu anda, farklı alma gecikmeleri ve maliyetler sunan, yüksek ve standart olan iki yeniden doldurma önceliklerini desteklemektedir. Daha fazla bilgi için bkz. [Arşiv katmanından blob verilerini yeniden doldurma](storage-blob-rehydration.md).

> [!NOTE]
> BLOB depolama hesapları, genel amaçlı v2 depolama hesaplarıyla aynı performans ve ölçeklenebilirlik hedeflerini destekler. Daha fazla bilgi için bkz. [BLOB depolama Için ölçeklenebilirlik ve performans hedefleri](scalability-targets.md).

## <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama

Tüm depolama hesapları, Blok Blobu depolama için her Blobun katmanını temel alan bir fiyatlandırma modeli kullanır. Aşağıdaki faturalandırma konularını göz önünde bulundurun:

- **Depolama maliyetleri**: Depolanan veri miktarına ek olarak, veri depolamanın maliyeti erişim katmanına bağlı olarak değişir. Katmanın erişim sıklığı düştükçe gigabayt başına ücret de azalır.
- **Veri erişimi maliyetleri**: Katmanın erişimi sıklığı düştükçe veri erişimi ücretleri artar. Seyrek erişimli ve arşiv erişim katmanındaki veriler için, okuma için gigabayt başına veri erişim ücreti üzerinden ücretlendirilirsiniz.
- **İşlem maliyetleri**: katman daha soğuk aldığından, artan tüm katmanların işlem başına ücreti vardır.
- **Coğrafi çoğaltma veri aktarımı maliyetleri**: Bu ücret yalnızca, GRS ve RA-GRS dahil olmak üzere, coğrafi çoğaltma yapılandırılmış hesaplara uygulanır. Coğrafi çoğaltma veri aktarımı gigabayt başına ücret doğurur.
- **Giden veri aktarımı maliyetleri**: Giden veri aktarımları (bir Azure bölgesinin dışına aktarılan veriler), genel amaçlı depolama hesapları ile tutarlı şekilde gigabayt başına esaslı olarak bant genişliği kullanımı için fatura doğurur.
- **Erişim katmanını değiştirme**: hesap erişim katmanını değiştirmek, açık katman kümesi olmayan tüm Bloblar için katman değişikliği ücretlerine neden olur. Tek bir Blobun erişim katmanını değiştirme hakkında daha fazla bilgi için bkz. [BLOB düzeyi katmanlama faturalaması](#blob-level-tiering-billing).

    Sürüm oluşturma etkinken bir blob için erişim katmanını değiştirme veya Blobun anlık görüntüler varsa, ek ücretler elde edebilir. Sürüm oluşturma özelliği etkinken blob 'lar hakkında daha fazla bilgi için bkz. blob sürüm oluşturma belgelerindeki [fiyatlandırma ve faturalandırma](versioning-overview.md#pricing-and-billing) . Anlık görüntülerle blob 'lar hakkında bilgi için bkz. blob anlık görüntüleri belgelerindeki [fiyatlandırma ve faturalandırma](snapshots-overview.md#pricing-and-billing) .

> [!NOTE]
> Blok Blobları için fiyatlandırma hakkında daha fazla bilgi için bkz. [Blok Blobu fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/blobs/). Giden veri aktarımı ücretleri hakkında daha fazla bilgi için bkz. [bant genişliği fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/bandwidth/) sayfası.

## <a name="availability"></a>Kullanılabilirlik

Blob düzeyi katmanlama ile birlikte farklı erişim katmanları, select bölgelerinde kullanılabilir. Tam liste için bkz. [Bölgelere göre kullanılabilir Azure ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Sonraki adımlar

Erişim katmanları genelinde Blobları ve hesapları yönetmeyi öğrenin.

- [Azure depolama hesabındaki bir Blobun katmanını yönetme](manage-access-tier.md)
- [Bir Azure depolama hesabının varsayılan hesap erişim katmanını yönetme](../common/manage-account-default-access-tier.md)
- [Azure Blob depolama erişim katmanlarını otomatikleştirerek maliyetleri iyileştirin](storage-lifecycle-management-concepts.md)
