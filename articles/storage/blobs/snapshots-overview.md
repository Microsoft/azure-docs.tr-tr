---
title: Blob anlık görüntüleri
titleSuffix: Azure Storage
description: Blob anlık görüntülerinin nasıl çalıştığını ve nasıl faturalandırıldığını anlayın.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/02/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: debce0a1b4c09bb89cdceb1cd29e59e1976c939a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539169"
---
# <a name="blob-snapshots"></a>Blob anlık görüntüleri

Anlık görüntü, bir Blobun zaman içinde alınmış bir salt okunurdur.

> [!NOTE]
> Blob sürümü oluşturma, bir Blobun önceki sürümlerini korumak için üstün bir yol sunar. Daha fazla bilgi için bkz. [BLOB sürümü oluşturma](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Blob anlık görüntüleri hakkında

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Blob URI 'si, blob URI 'sinin, anlık görüntünün alındığı saati belirtmek için blob URI 'sine eklenmiş bir **Tarih saat** değeri olması dışında, kendi temel blobuna benzer. Örneğin, bir Sayfa Blobu URI 'SI ise `http://storagesample.core.blob.windows.net/mydrives/myvhd` , anlık görüntü URI 'si öğesine benzerdir `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` .

> [!NOTE]
> Tüm anlık görüntüler, temel Blobun URI 'sini paylaşır. Temel blob ve anlık görüntü arasındaki tek ayrım, eklenen **Tarih saat** değeridir.

Blob herhangi bir sayıda anlık görüntüye sahip olabilir. Anlık görüntüler, bağımsız olarak ya da temel Blobun [BLOB silme](/rest/api/storageservices/delete-blob) işleminin bir parçası olarak açıkça silinene kadar kalır. Geçerli anlık görüntülerinizi izlemek için temel bloba ilişkili anlık görüntüleri numaralandırabilirsiniz.

Bir Blobun anlık görüntüsünü oluşturduğunuzda, Blobun sistem özellikleri aynı değerlerle anlık görüntüye kopyalanır. Temel Blobun meta verileri, oluşturduğunuz sırada anlık görüntü için ayrı meta veriler belirtmediğiniz müddetçe anlık görüntüye de kopyalanır. Anlık görüntü oluşturduktan sonra, bunu okuyabilir, kopyalayabilir veya silebilirsiniz, ancak değiştiremezsiniz.

Temel blob ile ilişkili tüm kiralamalar anlık görüntüyü etkilemez. Anlık görüntü üzerinde kira elde edilemez.

Bir VHD dosyası, bir VM diskinin geçerli bilgilerini ve durumunu depolamak için kullanılır. VM 'nin içinden bir diski ayırabilir veya VM 'yi kapatabilir ve sonra VHD dosyasının anlık görüntüsünü alabilirsiniz. Bu anlık görüntü dosyasını daha sonra bu noktada VHD dosyasını almak ve VM 'yi yeniden oluşturmak için kullanabilirsiniz.

## <a name="understand-how-snapshots-accrue-charges"></a>Anlık görüntülerin ücretleri nasıl tahakkuk ettir öğrenin

### <a name="important-billing-considerations"></a>Önemli faturalandırma konuları

Aşağıdaki liste, bir anlık görüntü oluştururken dikkate alınması gereken önemli noktaları içerir.

- Depolama hesabınız, blob 'ta mi yoksa anlık görüntüde olsun, benzersiz bloklar veya sayfalar için ücretler uygular. Hesabınız, temel aldıkları blobu güncelleştirene kadar bir blob ile ilişkili anlık görüntüler için ek ücret uygulamaz. Temel blobu güncelleştirdikten sonra, anlık görüntülerinden ayrılan olur. Bu durumda, her blob veya anlık görüntüde benzersiz bloklar veya sayfalar için ücretlendirilirsiniz.
- Blok Blobu içindeki bir bloğu değiştirdiğinizde, bu blok daha sonra benzersiz bir blok olarak ücretlendirilir. Bu, blok aynı blok KIMLIĞINE ve anlık görüntüdeki verilerle aynı verilere sahip olsa da geçerlidir. Blok yeniden gerçekleştirildikten sonra, herhangi bir anlık görüntüde onun karşılığından ayrılan bir işlem olur ve veriler için ücretlendirilirsiniz. Aynı verilerle güncelleştirilmiş bir sayfa blobunun bir sayfası için de aynı değer geçerlidir.
- Blob 'un tüm içeriğinin üzerine yazan bir yöntemi çağırarak bir blok Blobun güncelleştirilmesi, Blobun tüm blokların yerini alır. Bu bloba ilişkili bir anlık görüntü varsa, temel blob ve anlık görüntüdeki tüm bloklar artık birbirinden kalır ve her iki blobdaki tüm bloklar için ücretlendirilirsiniz. Bu, temel bloba ve anlık görüntüdeki veriler aynı kalabilse bile geçerlidir.
- Azure Blob hizmeti, iki Blobun aynı verileri içerip içermediğini belirleme anlamına gelir. Karşıya yüklenen ve yürütülen her bir blok, aynı verilere ve aynı blok KIMLIĞINE sahip olsa bile benzersiz olarak değerlendirilir. Ücretler benzersiz bloklar için tahakkuk ettiğinden, anlık görüntü sonucu olan bir Blobun ek benzersiz bloklar ve ek ücretler ile güncelleştirilmesini göz önünde bulundurmanız önemlidir.

### <a name="minimize-costs-with-snapshot-management"></a>Anlık görüntü yönetimi ile maliyetleri en aza indirme

Ek ücretlerden kaçınmak için anlık görüntülerinizi dikkatle yönetmeniz önerilir. Anlık görüntülerinizin depolanması tarafından tahakkuk eden maliyetleri en aza indirmeye yardımcı olması için bu en iyi yöntemleri izleyebilirsiniz:

- Aynı verilerle güncelleştirseniz bile blob 'u her güncelleştirdiğinizde bir blob ile ilişkili anlık görüntüleri silin ve yeniden oluşturun, çünkü uygulama tasarımınız anlık görüntüleri korumanızı gerektirmez. Blobun anlık görüntülerini silip yeniden oluşturarak Blobun ve anlık görüntülerin birbirinden yönlendirilmemesini sağlayabilirsiniz.
- Bir Blobun anlık görüntülerini yaşıyorsanız, blobu güncelleştirdiğinizde tüm Blobun üzerine yazmış olan yöntemlerin çağrılmasını önleyin. Bunun yerine, maliyetleri düşük tutmak için mümkün olan en az sayıda blok güncelleştirin.

### <a name="snapshot-billing-scenarios"></a>Anlık görüntü faturalandırma senaryoları

Aşağıdaki senaryolarda, giderlerin bir Blok Blobu ve anlık görüntüleri için nasıl tahakkuk olduğu gösterilmektedir.

## <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama

Bir Blobun salt okunurdur kopyası olan bir anlık görüntü oluşturmak, hesabınıza ek veri depolama ücretleri oluşmasına neden olabilir. Uygulamanızı tasarlarken, maliyetleri en aza indirmek için bu ücretlerin nasıl tahakkuk edebileceğini bilmeniz önemlidir.

Blob sürümleri gibi BLOB anlık görüntüleri, etkin verilerle aynı hızda faturalandırılır. Anlık görüntülerin faturalandırılması, temel Blobun katmanı veya anlık görüntülerinin (veya sürümlerinin) açıkça ayarlanmış olmasına bağlıdır. Blob katmanları hakkında daha fazla bilgi için bkz. [Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](storage-blob-storage-tiers.md).

Bir Blobun veya anlık görüntünün katmanını değiştirmediyseniz, o blob, anlık görüntüleri ve sahip olabileceği tüm sürümlerde verilerin benzersiz blokları üzerinden faturalandırılırsınız. Daha fazla bilgi için bkz. [BLOB katmanı açıkça ayarlanmamışsa faturalandırma](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Bir Blobun veya anlık görüntünün katmanını değiştirdiyseniz, blob ve anlık görüntünün son olarak aynı katmanda olup olmamasından bağımsız olarak tüm nesne için faturalandırılırsınız. Daha fazla bilgi için bkz. [BLOB katmanı açıkça ayarlandığında faturalama](#billing-when-the-blob-tier-has-been-explicitly-set).

Blob sürümlerinin faturalama ayrıntıları hakkında daha fazla bilgi için bkz. [BLOB sürümü oluşturma](versioning-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Blob katmanı açıkça ayarlanmamışsa faturalandırma

Bir temel blob veya onun anlık görüntülerinin blob katmanını açıkça ayarlamazsanız, blob, anlık görüntüleri ve sahip olabileceği tüm sürümlerde benzersiz bloklar veya sayfalar üzerinden ücretlendirilirsiniz. Blob ve anlık görüntüleri arasında paylaşılan veriler yalnızca bir kez ücretlendirilir. Bir blob güncelleştirilirken, bir temel blob 'daki veriler, anlık görüntülerle depolanan verilerden farklıdır ve benzersiz veriler blok veya sayfa başına ücretlendirilir.

Blok Blobu içindeki bir bloğu değiştirdiğinizde, bu blok daha sonra benzersiz bir blok olarak ücretlendirilir. Bu, blok aynı blok KIMLIĞINE ve anlık görüntüdeki verilerle aynı verilere sahip olsa da geçerlidir. Blok yeniden gerçekleştirildikten sonra, anlık görüntüdeki karşılık gelen karşılığına sahiptir ve verileri için ücretlendirilirsiniz. Aynı verilerle güncelleştirilmiş bir sayfa blobunun bir sayfası için de aynı değer geçerlidir.

Blob depolamada, iki Blobun aynı verileri içerip içermediğini belirleme yolu yoktur. Karşıya yüklenen ve yürütülen her bir blok, aynı verilere ve aynı blok KIMLIĞINE sahip olsa bile benzersiz olarak değerlendirilir. Ücretler benzersiz bloklar için tahakkuk ettiğinden, blob 'un anlık görüntülere veya sürümlere sahip olduğu durumlarda bir blob 'u güncellemeyi göz önünde bulundurmanız önemlidir.

Bir Blobun anlık görüntülere sahip olduğunda, blok Bloblarındaki güncelleştirme işlemlerini, en az olası blok sayısını güncelleştirecek şekilde çağırın. Bloklar üzerinde ayrıntılı denetime izin veren yazma işlemleri, [PUT bloğu](/rest/api/storageservices/put-block) ve [yerleştirme engellenenler listesi](/rest/api/storageservices/put-block-list)' dir. Diğer taraftan [BLOB 'U koy](/rest/api/storageservices/put-blob) işlemi, bir Blobun tüm içeriğini değiştirir ve bu nedenle ek ücretlere neden olabilir.

Aşağıdaki senaryolarda, blob katmanı açıkça ayarlanmamışsa bir Blok Blobu ve anlık görüntülerinin ücretlerinden nasıl tahakkuk olduğu gösterilmektedir.

#### <a name="scenario-1"></a>1\. Senaryo

Senaryo 1 ' de, anlık görüntü alındıktan sonra temel blob güncelleştirilmedi, bu nedenle ücretler yalnızca 1, 2 ve 3 benzersiz bloklar için ücretlendirilir.

![Temel blob ve anlık görüntüdeki benzersiz bloklar için faturalandırmayı gösteren diyagram 1.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>2\. Senaryo

Senaryo 2 ' de, temel blob güncelleştirildi, ancak anlık görüntü yok. 2. blok güncelleştirildi ve aynı verileri ve aynı KIMLIĞI içerse de, anlık görüntüde blok 3 ile aynı değildir. Sonuç olarak, hesap dört blok için ücretlendirilir.

![Temel blob ve anlık görüntüdeki benzersiz bloklar için faturalandırmayı gösteren diyagram 2.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>3\. Senaryo

Senaryo 3 ' te, temel blob güncelleştirildi, ancak anlık görüntü yok. Blok 3, temel Blobun içindeki blok 4 ile değiştirilmiştir, ancak anlık görüntü hala blok 3 ' ü yansıtır. Sonuç olarak, hesap dört blok için ücretlendirilir.

![Temel blob ve anlık görüntüdeki benzersiz bloklar için faturalandırmayı gösteren diyagram 3.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>4\. Senaryo

Senaryo 4 ' te, temel blob tamamen güncelleştirilmiştir ve özgün bloklarından hiçbirini içermez. Sonuç olarak, hesap tüm sekiz benzersiz blok için ücretlendirilir.

![Temel blob ve anlık görüntüdeki benzersiz bloklar için faturalandırmayı gösteren diyagram 4.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> Tüm Blobun üzerine yazacak yöntemlerin çağrılmasını önleyin ve bunun yerine maliyetleri düşük tutmak için ayrı blokları güncelleştirin.

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Blob katmanı açıkça ayarlandığında faturalandırma

Blob katmanını bir blob veya anlık görüntü (veya sürüm) için açık olarak ayarladıysanız, yeni katmanda nesnenin tam içerik uzunluğu için ücretlendirilirsiniz. Bu durumda, blokları orijinal katmanda bir nesne ile paylaştığından bağımsız olarak. Orijinal katmanda en eski sürümün tam içerik uzunluğu için de ücretlendirilirsiniz. Özgün katmanda kalan sürümler veya anlık görüntüler, [BLOB katmanı açıkça ayarlanmamışsa faturalandırma](#billing-when-the-blob-tier-has-not-been-explicitly-set)bölümünde açıklandığı gibi, PAYLAŞILABİLECEKLERİ benzersiz bloklar için ücretlendirilir.

#### <a name="moving-a-blob-to-a-new-tier"></a>Bir blobu yeni katmana taşıma

Aşağıdaki tabloda, yeni katmana taşındığında bir blob veya anlık görüntü için faturalandırma davranışı açıklanmaktadır.

| Blob katmanı açık olarak ayarlandığında... | Ardından, için faturalandırılırsınız... |
|-|-|
| Anlık görüntü içeren bir temel blob | Yeni katmandaki temel blob ve orijinal katmandaki en eski anlık görüntü ve diğer anlık görüntüdeki tüm benzersiz bloklar. <sup>1</sup> |
| Önceki sürüme ve anlık görüntüye sahip bir temel blob | Yeni katmandaki temel blob, orijinal katmandaki en eski sürüm ve orijinal katmandaki en eski anlık görüntü ve diğer sürümlerdeki veya anlık görüntü<sup>1</sup>' deki tüm benzersiz bloklar. |
| Anlık görüntü | Yeni katmandaki anlık görüntü ve orijinal katmandaki temel blob ve diğer anlık görüntüdeki tüm benzersiz bloklar. <sup>1</sup> |

<sup>1</sup> Özgün katmanlarından taşınmayan başka bir önceki sürüm veya anlık görüntü varsa, bu sürümler veya anlık görüntüler, [BLOB katmanı açıkça ayarlanmamışsa faturalandırma](#billing-when-the-blob-tier-has-not-been-explicitly-set)bölümünde açıklandığı gibi, içerdikleri benzersiz blok sayısına göre ücretlendirilir.

Aşağıdaki diyagramda, anlık görüntülere sahip bir blob farklı bir katmana taşındığında nesnelerin nasıl faturalandırılabileceği gösterilmektedir.

:::image type="content" source="media/snapshots-overview/snapshot-billing-tiers.png" alt-text="Anlık görüntülere sahip bir blob açık şekilde katmanlandığı zaman nesnelerin nasıl faturalandırıldığını gösteren diyagram.":::

Bir blob, sürüm veya anlık görüntü için katmanı açıkça ayarlamak geri alınamaz. Bir blobu yeni bir katmana taşır ve sonra özgün katmanına geri taşırsanız, özgün katmandaki diğer nesnelerle blokları paylaşsa bile nesnenin tam içerik uzunluğu için ücretlendirilirsiniz.

Blob, sürüm veya anlık görüntünün katmanını açıkça ayarlamış işlemler şunlardır:

- [Blob Katmanını Ayarla](/rest/api/storageservices/set-blob-tier)
- [Blobu](/rest/api/storageservices/put-blob) belirtilen katmana yerleştir
- Belirtilen katmana sahip [blok listesini yerleştir](/rest/api/storageservices/put-block-list)
- Belirtilen katmana sahip [blobu Kopyala](/rest/api/storageservices/copy-blob)

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Geçici silme etkinken bir blobu silme

Blob geçici silme etkinleştirildiğinde, katmanı açıkça ayarlanmış bir temel blobu siler veya üzerine yazdığınızda, geçici olarak silinen Blobun önceki sürümleri veya anlık görüntüleri tam içerik uzunluğuna göre faturalandırılır. Blob sürümü oluşturma ve geçici silme işlemlerinin birlikte çalışması hakkında daha fazla bilgi için bkz. [BLOB sürümü oluşturma ve geçici silme](versioning-overview.md#blob-versioning-and-soft-delete).

Aşağıdaki tabloda, sürüm oluşturma 'nın etkin veya devre dışı olmasına bağlı olarak, geçici olarak silinen bir blob için faturalandırma davranışı açıklanmaktadır. Sürüm oluşturma etkinken, bir blob geçici olarak silindiğinde yeni bir sürüm oluşturulur. Sürüm oluşturma devre dışı bırakıldığında, bir blobu geçici olarak silmek, yumuşak silme anlık görüntüsü oluşturur.

| Katman açıkça ayarlanmış bir temel Blobun üzerine yazdığınızda... | Ardından, için faturalandırılırsınız... |
|-|-|
| Blob geçici silme ve sürüm oluşturma özelliği etkinse | Katman ne olursa olsun, tüm mevcut sürümler tam içerik uzunluğuna sahiptir. |
| Blob geçici silme etkinse ancak sürüm oluşturma devre dışıysa | Katmanından bağımsız olarak tam içerik uzunluğundaki tüm mevcut geçici ekran anlık görüntüleri. |

## <a name="next-steps"></a>Sonraki adımlar

- [Blob sürümü oluşturma](versioning-overview.md)
- [.NET 'te blob anlık görüntüsü oluşturma ve yönetme](snapshots-manage-dotnet.md)
- [Artımlı anlık görüntülerle Azure yönetilmeyen VM disklerini yedekleme](../../virtual-machines/windows/incremental-snapshots.md)
