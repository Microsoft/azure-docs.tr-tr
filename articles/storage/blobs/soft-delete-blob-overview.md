---
title: Bloblar için geçici silme
titleSuffix: Azure Storage
description: Blob 'lar için geçici silme, verilerinizi korur, böylece bir uygulama veya başka bir depolama hesabı kullanıcısı tarafından yanlışlıkla değiştirildiğinde veya silindiğinde verilerinizi daha kolay bir şekilde kurtarabilirsiniz.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: ef1ed584a609b2e4baa27111e47343df99146f5a
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257509"
---
# <a name="soft-delete-for-blobs"></a>Bloblar için geçici silme

Blob geçici silme, belirli bir süre boyunca silinen verileri korumak için bağımsız bir blob, anlık görüntü veya sürümü yanlışlıkla silme veya üzerine yazma işlemlerinin korunmasını sağlar. Saklama süresi boyunca, geçici olarak silinen bir nesneyi silindiği sırada durumuna geri yükleyebilirsiniz. Saklama süresi dolduktan sonra nesne kalıcı olarak silinir.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="recommended-data-protection-configuration"></a>Önerilen veri koruma yapılandırması

Blob geçici silme, blob verileri için kapsamlı bir veri koruma stratejisinin bir parçasıdır. Blob verileriniz için en iyi koruma için, Microsoft aşağıdaki veri koruma özelliklerinin tümünü etkinleştirmeyi önerir:

- Silinen bir kapsayıcıyı geri yüklemek için kapsayıcı geçici silme. Kapsayıcı geçici silmeyi etkinleştirme hakkında bilgi edinmek için bkz. [kapsayıcılar için geçici silmeyi etkinleştirme ve yönetme](soft-delete-container-enable.md).
- Blob 'un önceki sürümlerini otomatik olarak sürdürmek için blob sürümü oluşturma. Blob sürümü oluşturma etkinleştirildiğinde, yanlışlıkla değiştirildiyse veya silinirse verilerinizi kurtarmak için bir Blobun önceki bir sürümünü geri yükleyebilirsiniz. Blob sürümü oluşturmayı nasıl etkinleştireceğinizi öğrenmek için bkz. [BLOB sürüm oluşturmayı etkinleştirme ve yönetme](versioning-enable.md).
- Blob geçici silme, silinen bir blobu, anlık görüntüyü veya sürümü geri yüklemek için. Blob geçici silme özelliğini etkinleştirmeyi öğrenmek için bkz. [Bloblar için geçici silmeyi etkinleştirme ve yönetme](soft-delete-blob-enable.md).

Microsoft 'un veri koruma önerileri hakkında daha fazla bilgi edinmek için bkz. [veri korumasına genel bakış](data-protection-overview.md).

## <a name="how-blob-soft-delete-works"></a>Blob geçici silme nasıl kullanılır?

Bir depolama hesabı için blob geçici silmeyi etkinleştirdiğinizde, 1 ila 365 gün arasında silinen nesneler için bir saklama süresi belirlersiniz. Saklama süresi, verilerin silinip üzerine yazıldıktan sonra ne kadar süreyle kullanılabilir kaldığını gösterir. Saat, bir nesne silindiği veya üzerine yazıldığı anda bekletme döneminde başlar.

Saklama süresi etkin olsa da, geri alma [blobu](/rest/api/storageservices/undelete-blob) işlemini çağırarak silinen bir blobu anlık görüntülerle veya silinen bir sürümle birlikte geri yükleyebilirsiniz. Aşağıdaki diyagramda, blob geçici silme etkinken silinen bir nesnenin nasıl geri yüklenebildiğinden gösterilmektedir:

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="Geçici olarak silinen bir Blobun nasıl geri yükleneolabileceğini gösteren diyagram":::

Geçici silme bekletme süresini istediğiniz zaman değiştirebilirsiniz. Güncelleştirilmiş bir bekletme dönemi yalnızca, saklama dönemi değiştirildikten sonra silinen veriler için geçerlidir. Saklama süresi değiştirilmeden önce silinen tüm veriler, silindiğinde geçerli olan saklama süresine tabidir.

Geçici olarak silinen bir nesneyi silmeye çalışmak, süre sonu süresini etkilemez.

Blob geçici silme devre dışı bırakırsanız, geçici silme bekletme süresi geçene kadar depolama hesabınızdaki geçici olarak silinen nesnelere erişmeye ve kurtarmaya devam edebilirsiniz.

Blob sürümü oluşturma genel amaçlı v2, Blok Blobu ve BLOB depolama hesapları için kullanılabilir. Azure Data Lake Storage 2. ile kullanım için etkinleştirilmiş hiyerarşik bir ad alanı olan depolama hesapları Şu anda desteklenmemektedir.

Sürüm 2017-07-29 ve Azure Storage 'ın üstü REST API blob geçici silmeyi destekler.

> [!IMPORTANT]
> Tek bir blobu, anlık görüntüyü veya sürümü geri yüklemek için yalnızca blob geçici silme özelliğini kullanabilirsiniz. Bir kapsayıcıyı ve içeriğini geri yüklemek için, depolama hesabı için kapsayıcı geçici silme özelliğinin de etkinleştirilmesi gerekir. Microsoft, blob verilerine yönelik tüm korumanın korunmasını sağlamak için blob geçici silme ve BLOB sürüm oluşturma 'yı blob özellikli silme ile birlikte etkinleştirmeyi önerir. Daha fazla bilgi için bkz. [veri korumasına genel bakış](data-protection-overview.md).
>
> Blob geçici silme, bir depolama hesabının silinmesine karşı koruma sağlamaz. Bir depolama hesabını silinmeye karşı korumak için, depolama hesabı kaynağında bir kilit yapılandırın. Depolama hesabını kilitleme hakkında daha fazla bilgi için bkz. [depolama hesabına Azure Resource Manager kilidi uygulama](../common/lock-account-resource.md).

### <a name="how-deletions-are-handled-when-soft-delete-is-enabled"></a>Geçici silme etkinken silme işlemleri nasıl işlenir

Blob geçici silme etkin olduğunda, bir blobu silme işlemi bir blob tarafından geçici olarak silinmiş olarak işaretlenir. Anlık görüntü oluşturulmaz. Saklama süresi sona erdiğinde, geçici olarak silinen blob kalıcı olarak silinir.

Bir Blobun anlık görüntüler varsa, anlık görüntüler de silinmedikleri takdirde blob silinemez. Bir blobu ve anlık görüntülerini sildiğinizde, hem blob hem de anlık görüntüler geçici olarak silinmiş olarak işaretlenir. Yeni anlık görüntü oluşturulmaz.

Ayrıca, temel blobu silmeden bir veya daha fazla etkin anlık görüntüyü silebilirsiniz. Bu durumda, anlık görüntü geçici olarak silinir.

Geçici olarak silinen nesneler, açıkça görüntülenmediği veya listelenmedikleri takdirde görünmez. Geçici olarak silinen nesneleri listeleme hakkında daha fazla bilgi için bkz. [geçici olarak silinen Blobları yönetme ve geri yükleme](soft-delete-blob-manage.md).

### <a name="how-overwrites-are-handled-when-soft-delete-is-enabled"></a>Geçici silme etkinken üzerine yazma işlemleri nasıl işlenir

[BLOB koyma](/rest/api/storageservices/put-blob), [yerleştirme listesi](/rest/api/storageservices/put-block-list)veya [kopyalama](/rest/api/storageservices/copy-blob) blobu gibi bir işlemin çağrılması bir Blobun verilerin üzerine yazar. Blob geçici silme etkinleştirildiğinde, bir Blobun üzerine yazılması, blob 'un yazma işleminden önce geçici olarak silinen bir anlık görüntüsünü otomatik olarak oluşturur. Saklama süresi sona erdiğinde, geçici olarak silinen anlık görüntü kalıcı olarak silinir.

Geçici olarak silinen nesneler açıkça görüntülenmiyorsa veya listelenmediyse, geçici olarak silinen anlık görüntüler görünmez. Geçici olarak silinen nesneleri listeleme hakkında daha fazla bilgi için bkz. [geçici olarak silinen Blobları yönetme ve geri yükleme](soft-delete-blob-manage.md).

Bir kopyalama işlemini korumak için, hedef depolama hesabı için blob geçici silme özelliğinin etkinleştirilmesi gerekir.

Blob geçici silme, blob meta verileri veya özellikleri yazma işlemlerine karşı koruma sağlamaz. Bir Blobun meta verileri veya özellikleri güncelleştirilirken geçici olarak silinen anlık görüntü oluşturulmaz.

Blob geçici silme, arşiv katmanındaki Bloblar için korumanın üzerine yazmaz. Arşiv katmanındaki bir Blobun herhangi bir katmanda yeni bir blob ile üzerine yazılırsa, üzerine yazılan blob kalıcı olarak silinir.

Premium Depolama hesaplarında, geçici olarak silinen anlık görüntüler 100 anlık görüntü için blob başına doğru sayılmaz.

### <a name="restoring-soft-deleted-objects"></a>Geçici olarak silinen nesneleri geri yükleme

Saklama süresi içinde geri alma [blobu](/rest/api/storageservices/undelete-blob) işlemini çağırarak, geçici olarak silinen Blobları geri yükleyebilirsiniz. **Geri alma blobu** işlemi, bir blobu ve onunla ilişkili olan geçici olarak silinen anlık görüntüleri geri yükler. Saklama süresi boyunca silinen tüm anlık görüntüler geri yüklenir.

Geçici olarak silinmiş olmayan bir blob üzerinde **geri alma blobu** çağırmak, Blobun ilişkili olan geçici olarak silinen anlık görüntüleri geri yükler. Blobun anlık görüntü yoksa ve geçici olarak silinirse, **geri alma blobunun** çağrılması hiçbir etkiye sahip olmaz.

Geçici olarak silinen bir anlık görüntüyü temel blob 'a yükseltmek için, önce blob 'u ve anlık görüntülerini geri yüklemek için temel bloba geri alma **blobu** çağırın. Sonra, istenen anlık görüntüyü temel blob üzerine kopyalayın. Ayrıca, anlık görüntüyü yeni bir bloba kopyalayabilirsiniz.

Geçici olarak silinen bir blob veya anlık görüntüdeki veriler, nesne geri yüklenene kadar okunamaz.

Geçici olarak silinen nesneleri geri yükleme hakkında daha fazla bilgi için bkz. [geçici olarak silinen Blobları yönetme ve geri yükleme](soft-delete-blob-manage.md).

## <a name="blob-soft-delete-and-versioning"></a>Blob geçici silme ve sürüm oluşturma

Blob sürümü oluşturma ve BLOB geçici silme her ikisi de bir depolama hesabı için etkinleştirildiyse, bir Blobun üzerine yazılması otomatik olarak yeni bir sürüm oluşturur. Yeni sürüm geçici olarak silinmez ve geçici silme bekletme süresi sona erdiğinde kaldırılmaz. Geçici olarak silinen anlık görüntü oluşturulmaz. Bir blobu sildiğinizde, blob 'un geçerli sürümü önceki bir sürüm olur ve artık geçerli bir sürüm değildir. Yeni sürüm oluşturulmaz ve geçici olarak silinen anlık görüntü oluşturulmaz.

Geçici silme ve sürüm oluşturma özelliğinin birlikte etkinleştirilmesi, blob sürümlerini silme işleminden korur. Geçici silme etkinleştirildiğinde, bir sürümü silmek, geçici olarak silinen bir sürüm oluşturur. Geçici silme Bekletme dönemi sırasında geçici olarak silinen sürümleri geri yüklemek için silme **blobu** işlemini kullanabilirsiniz. **Geri alma blobu** işlemi, blob 'un tüm geçici silinen sürümlerini her zaman geri yükler. Yalnızca tek bir geçici olarak silinen sürümü geri yüklemek mümkün değildir.

Geçici silme bekletme süresi geçtikten sonra, geçici olarak silinen blob sürümleri kalıcı olarak silinir.

> [!NOTE]
> Sürüm oluşturma etkinken silinen blob üzerinde **geri alma blobu** işleminin çağrılması, geçici olarak silinen sürümleri veya anlık görüntüleri geri yükler, ancak geçerli sürümü geri yüklemez. Geçerli sürümü geri yüklemek için, önceki bir sürümü güncel sürüme kopyalayarak yükseltin.

Microsoft, en iyi veri koruması için depolama hesaplarınız için hem sürüm oluşturma hem de blob geçici silmeyi etkinleştirmeyi önerir. Blob sürümü oluşturma ve geçici silme hakkında daha fazla bilgi için bkz. [BLOB sürümü oluşturma ve geçici silme](versioning-overview.md#blob-versioning-and-soft-delete).

## <a name="blob-soft-delete-protection-by-operation"></a>Blob, işleme göre geçici silme koruması

Aşağıdaki tabloda blob özellikli silme etkinken silme ve yazma işlemleri için beklenen davranışın yanı sıra blob sürümü oluşturma ile veya olmayan bir şekilde açıklanmaktadır:

| REST API işlemler | Geçici silme etkin | Geçici silme ve sürüm oluşturma etkin |
|--|--|--|
| [Depolama hesabını Sil](/rest/api/storagerp/storageaccounts/delete) | Bir değişiklik olmadı. Silinen hesaptaki kapsayıcılar ve Bloblar kurtarılamaz. | Bir değişiklik olmadı. Silinen hesaptaki kapsayıcılar ve Bloblar kurtarılamaz. |
| [Kapsayıcıyı Silme](/rest/api/storageservices/delete-container) | Bir değişiklik olmadı. Silinen kapsayıcıdaki Bloblar kurtarılamaz. | Bir değişiklik olmadı. Silinen kapsayıcıdaki Bloblar kurtarılamaz. |
| [İkili Büyük Nesneyi Silme](/rest/api/storageservices/delete-blob) | Bir blobu silmek için kullanılırsa, bu blob geçici olarak silindi olarak işaretlenir. <br /><br /> Blob anlık görüntüsünü silmek için kullanılırsa, anlık görüntü geçici olarak silinmiş olarak işaretlenir. | Bir blobu silmek için kullanılırsa, geçerli sürüm önceki bir sürüm olur ve geçerli sürüm silinir. Yeni sürüm oluşturulmaz ve geçici olarak silinen anlık görüntü oluşturulmaz.<br /><br /> Blob sürümünü silmek için kullanılırsa, sürüm geçici olarak silindi olarak işaretlenir. |
| [Blobu geri al](/rest/api/storageservices/delete-blob) | Bir blobu ve Bekletme dönemi içinde silinen tüm anlık görüntüleri geri yükler. | Bir blobu ve Bekletme dönemi içinde silinen tüm sürümleri geri yükler. |
| [İkili Büyük Nesne Koyma](/rest/api/storageservices/put-blob)<br />[Öbek listesini yerleştirme](/rest/api/storageservices/put-block-list)<br />[İkili Büyük Nesneyi Kopyalama](/rest/api/storageservices/copy-blob)<br />[Blob 'U URL 'den Kopyala](/rest/api/storageservices/copy-blob) | Etkin bir blob üzerinde çağrılırsa, işlemden önce blob 'un durumunun bir anlık görüntüsü otomatik olarak oluşturulur. <br /><br /> Geçici olarak silinen bir blob üzerinde çağrılırsa, blob 'un önceki durumunun bir anlık görüntüsü yalnızca aynı türdeki bir blob ile değiştiriliyorsa oluşturulur. Blob farklı bir türde ise, tüm mevcut geçici silinen veriler kalıcı olarak silinir. | İşlemden önce Blobun durumunu yakalayan yeni bir sürüm otomatik olarak oluşturulur. |
| [Yerleştirme bloğu](/rest/api/storageservices/put-block) | Etkin bir bloba bir blok yürütmek için kullanılırsa değişiklik yapılmaz.<br /><br />Geçici olarak silinen bir Blobun bir bloğu yürütmek için kullanılırsa, yeni bir blob oluşturulur ve geçici olarak silinen Blobun durumunu yakalamak için bir anlık görüntü oluşturulur. | Bir değişiklik olmadı. |
| [Yerleştirme sayfası](/rest/api/storageservices/put-page)<br />[URL 'den sayfa koy](/rest/api/storageservices/put-page-from-url) | Bir değişiklik olmadı. Bu işlem kullanılarak üzerine yazılan veya temizlenmemiş Sayfa Blobu verileri kaydedilmez ve kurtarılamaz. | Bir değişiklik olmadı. Bu işlem kullanılarak üzerine yazılan veya temizlenmemiş Sayfa Blobu verileri kaydedilmez ve kurtarılamaz. |
| [Ekleme bloğu](/rest/api/storageservices/append-block)<br />[URL 'den ekleme bloğu](/rest/api/storageservices/append-block-from-url) | Bir değişiklik olmadı. | Bir değişiklik olmadı. |
| [Blob özelliklerini ayarla](/rest/api/storageservices/set-blob-properties) | Bir değişiklik olmadı. Üzerine yazılan blob özellikleri kurtarılabilir değil. | Bir değişiklik olmadı. Üzerine yazılan blob özellikleri kurtarılabilir değil. |
| [Blob meta verilerini ayarla](/rest/api/storageservices/set-blob-metadata) | Bir değişiklik olmadı. Üzerine yazılan blob meta verileri kurtarılamaz. | İşlemden önce Blobun durumunu yakalayan yeni bir sürüm otomatik olarak oluşturulur. |
| [Blob Katmanını Ayarla](/rest/api/storageservices/set-blob-tier) | Temel blob yeni katmana taşınır. Etkin veya geçici olarak silinen tüm anlık görüntüler orijinal katmanda kalır. Geçici olarak silinen anlık görüntü oluşturulmaz. | Temel blob yeni katmana taşınır. Etkin veya geçici olarak silinen tüm sürümler orijinal katmanda kalır. Yeni sürüm oluşturulmadı. |

## <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama

Tüm geçici silinen veriler, etkin verilerle aynı hızda faturalandırılır. Saklama süresi dolduktan sonra kalıcı olarak silinen veriler için ücretlendirilmeyecektir.

Geçici silmeyi etkinleştirdiğinizde, Microsoft, özelliğin faturanızı nasıl etkileyeceğini daha iyi anlamak için kısa bir bekletme süresi kullanılmasını önerir. Önerilen en düşük saklama süresi yedi gündür.

Sık sık üzerine yazılan veriler için geçici silme özelliğinin etkinleştirilmesi, depolama kapasitesi ücretlerine neden olabilir ve Bloblar listelenirken gecikme süresini artırabilir. Sık kullanılan verileri, geçici olarak silmenin devre dışı bırakıldığı ayrı bir depolama hesabında depolayarak bu ek maliyeti ve gecikmeyi azaltabilirsiniz.

Bir blob üzerine yazıldığında veya silindiğinde otomatik olarak anlık görüntü veya Sürüm oluşturmayla ilgili işlemler için faturalandırılırsınız. Yazma işlemleri için işlem hızında geri alma **blobu** işlemine yapılan çağrılar için faturalandırılırsınız.

Blob Depolama fiyatlandırması hakkında daha fazla bilgi için bkz. [BLOB Storage fiyatlandırma](https://azure.microsoft.com/pricing/details/storage/blobs/) sayfası.

## <a name="blob-soft-delete-and-virtual-machine-disks"></a>Blob geçici silme ve sanal makine diskleri  

Blob geçici silme, hem Premium hem de standart yönetilmeyen diskler için kullanılabilir ve bu da, kapsamakta olan sayfa Blobları vardır. Geçici silme, **BLOB silme**, **BLOB koyma**, **PUT blok listesi** ve yalnızca **BLOB işlemlerini kopyalama** ile silinen veya üzerine yazılan verileri kurtarmanıza yardımcı olabilir.

**PUT sayfası** çağrısıyla üzerine yazılan veriler kurtarılamaz. Azure sanal makinesi, **yerleştirme** için çağrılar kullanılarak yönetilmeyen bir diske yazar. bu nedenle, Azure VM 'den yönetilmeyen bir diske yazma işlemini geri almak için geçici silme kullanmak desteklenen bir senaryo değildir.

## <a name="next-steps"></a>Sonraki adımlar

- [Bloblar için geçici silmeyi etkinleştirme](./soft-delete-blob-enable.md)
- [Geçici olarak silinen Blobları yönetme ve geri yükleme](soft-delete-blob-manage.md)
- [Blob sürümü oluşturma](versioning-overview.md)