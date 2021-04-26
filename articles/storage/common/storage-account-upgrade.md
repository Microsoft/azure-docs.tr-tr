---
title: Genel amaçlı v2 depolama hesabına yükseltme
titleSuffix: Azure Storage
description: Azure portal, PowerShell veya Azure CLı kullanarak genel amaçlı v2 depolama hesaplarına yükseltin. Blob verileri için bir erişim katmanı belirtin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: d6d94efea52d920b8bfe69be2ee07c8b829792d8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484107"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Genel amaçlı v2 depolama hesabına yükseltme

Genel amaçlı v2 depolama hesapları, en son Azure depolama özelliklerini destekler ve genel amaçlı v1 ve BLOB depolama hesaplarının tüm işlevlerini dahil edin. Genel amaçlı v2 hesapları çoğu depolama senaryosunda önerilir. Genel amaçlı v2 hesapları, Azure depolama için en düşük gigabayt başına kapasite fiyatlarını ve sektör rekabetçi işlem fiyatlarını sunar. Genel amaçlı v2 hesapları, sık erişimli, seyrek erişimli veya Arşiv arasında sık erişimli veya seyrek erişimli ve BLOB düzeyinde katmanlama varsayılan hesap erişim katmanlarını destekler.

Genel amaçlı v1 veya blob depolama hesaplarınızdan genel amaçlı v2 depolama hesabına yükseltme işlemi basittir. Azure portal, PowerShell veya Azure CLı kullanarak yükseltebilirsiniz. Genel amaçlı v2 depolama hesabına yükseltmeyle ilişkili bir kesinti veya veri kaybı riski yoktur. Hesap yükseltme, hesap türünü değiştiren basit bir Azure Resource Manager işlem aracılığıyla gerçekleşir.

> [!IMPORTANT]
> Genel amaçlı v1 veya blob depolama hesabını genel amaçlı v2 'ye yükseltme kalıcı olur ve geri alınamaz.

> [!NOTE]
> Microsoft çoğu senaryo için genel amaçlı v2 hesapları önerse de, Microsoft yeni ve mevcut müşteriler için genel amaçlı v1 hesaplarını desteklemeye devam edecektir. Azure Storage bu bölgelerde kullanılabilir olduğunda, yeni bölgelerde genel amaçlı v1 depolama hesapları oluşturabilirsiniz. Microsoft 'un, genel amaçlı v1 hesapları için desteği kullanımdan kaldırmaya yönelik bir planı yoktur ve herhangi bir Azure depolama özelliğinin kullanım dışı bırakılmadan önce en az bir yıllık ön uyarı sağlar. Microsoft, genel amaçlı v1 hesapları için güvenlik güncelleştirmeleri sağlamaya devam edecek, ancak bu hesap türü için yeni bir özellik geliştirmesi beklenmez.
>
> 1 Ekim 2020 ' den sonra çevrimiçi olan yeni Azure bölgeleri için, genel amaçlı v1 hesapları için fiyatlandırma değişmiştir ve bu bölgelerdeki genel amaçlı v2 hesapları için fiyatlandırmaya eşittir. 1 Ekim 2020 ' den önceki Azure bölgelerindeki genel amaçlı v1 hesapları fiyatlandırması değişmemiştir. Belirli bir bölgedeki genel amaçlı v1 hesapları için fiyatlandırma ayrıntıları için bkz. Azure Depolama fiyatlandırması sayfası. Bölgenizi seçin ve ardından **fiyatlandırma teklifleri**' nin yanındaki **diğer**' i seçin.

## <a name="upgrade-an-account"></a>Bir hesabı yükseltme

Genel amaçlı v1 veya blob depolama hesabını genel amaçlı v2 hesabına yükseltmek için Azure portal, PowerShell veya Azure CLı kullanın.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Depolama hesabınıza gidin.
3. **Ayarlar** bölümünde **yapılandırma**' ya tıklayın.
4. **Hesap türü** altında, **Yükselt**' e tıklayın.
5. **Yükseltmeyi Onayla** altında, hesabınızın adını yazın.
6. Dikey pencerenin alt kısmındaki **Yükselt** ' e tıklayın.

    ![Yükseltme hesabı türü](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell kullanarak genel amaçlı bir v1 hesabını genel amaçlı v2 hesabına yükseltmek için, önce PowerShell 'i **az. Storage** modülünün en son sürümünü kullanacak şekilde güncelleştirin. PowerShell’i yükleme hakkında bilgi edinmek için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-Az-ps).

Ardından, hesabı yükseltmek için, kaynak grubu adı, depolama hesabı adı ve istenen hesap erişim katmanınızı değiştirerek aşağıdaki komutu çağırın.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Genel amaçlı v1 hesabını Azure CLı kullanarak genel amaçlı v2 hesabına yükseltmek için önce Azure CLı 'nın en son sürümünü yüklemeniz gerekir. CLI yüklemesi hakkında bilgi için bkz. [Azure CLI 2.0’ı yükleme](/cli/azure/install-azure-cli).

Ardından, hesabı yükseltmek için, kaynak grubu adı, depolama hesabı adı ve istenen hesap erişim katmanınızı değiştirerek aşağıdaki komutu çağırın.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Blob verileri için bir erişim katmanı belirtin

Genel amaçlı v2 hesapları tüm Azure depolama hizmetleri ve veri nesnelerini destekler, ancak erişim katmanları yalnızca BLOB depolama alanındaki blok Bloblar için kullanılabilir. Genel amaçlı v2 depolama hesabına yükselttiğinizde, tek bir blob erişim katmanı parametresi belirtilmediyse blob verilerinizin karşıya yükleneceği varsayılan katmanı belirten, varsayılan bir hesap erişim katmanı olan sık erişimli veya seyrek erişimli ' i belirtebilirsiniz.

Blob erişim katmanları, tahmin edilen kullanım desenlerinize göre en düşük maliyetli depolamayı seçmenizi sağlar. Blok Blobları, sık erişimli, seyrek erişimli veya arşiv katmanlarında depolanabilir. Erişim katmanları hakkında daha fazla bilgi için bkz. [Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv depolama katmanları](../blobs/storage-blob-storage-tiers.md).

Varsayılan olarak, etkin erişim katmanında yeni bir depolama hesabı oluşturulur ve genel amaçlı v1 depolama hesabı, sık veya seyrek erişimli hesap katmanına yükseltilebilir. Yükseltme sırasında bir hesap erişim katmanı belirtilmemişse, varsayılan olarak etkin ' e yükseltilir. Yükseltmeniz için hangi erişim katmanının kullanılacağını araştırıyorsanız, geçerli veri kullanımı senaryonuzu değerlendirin. Genel amaçlı v2 hesabına geçiş için iki tipik Kullanıcı senaryosu vardır:

* Genel amaçlı bir v1 depolama hesabınız var ve genel amaçlı v2 depolama hesabına yükseltmeyi, blob verileri için doğru depolama erişim katmanıyla birlikte değerlendirmek istiyorsunuz.
* Genel amaçlı v2 depolama hesabı kullanmaya karar verdiniz veya zaten bir tane var ve blob verileri için sık veya seyrek erişimli depolama erişim katmanını kullanmanız gerekip gerekmediğini değerlendirmek istediğinizi değerlendirin.

Her iki durumda da ilk öncelik, genel amaçlı v2 depolama hesabında depolanan verilerinize depolama, erişme ve bu verileri çalıştırma maliyetini tahmin etmek ve bunu geçerli maliyetlerle karşılaştırmaktır.

## <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama

Bir v1 depolama hesabını genel amaçlı v2 hesabına yükseltmek ücretsizdir. Yükseltme işlemi sırasında istenen hesap katmanını belirtebilirsiniz. Yükseltme sırasında bir hesap katmanı belirtilmemişse, yükseltilen hesabın varsayılan hesap katmanı olur `Hot` . Bununla birlikte, yükseltme sonrasında depolama erişim katmanını değiştirmek faturanızda değişikliklere neden olabilir, böylece yükseltme sırasında yeni hesap katmanının belirtilmesi önerilir.

Tüm depolama hesapları, blob depolama için her blobun katmanını temel alan bir fiyatlandırma modelini kullanır. Bir depolama hesabını kullanırken aşağıdaki fatura değerlendirmeleri geçerlidir:

* **Depolama maliyetleri**: depolanan veri miktarına ek olarak, veri depolamanın maliyeti depolama erişim katmanına göre farklılık gösterir. Katmanın erişim sıklığı düştükçe gigabayt başına ücret de azalır.

* **Veri erişimi maliyetleri**: Katmanın erişimi sıklığı düştükçe veri erişimi ücretleri artar. Seyrek erişimli ve arşiv depolama erişim katmanındaki veriler için, okuma için gigabayt başına veri erişim ücreti üzerinden ücretlendirilirsiniz.

* **İşlem maliyetleri**: Tüm katmanlarda, erişim sıklığı düştükçe artan bir işlem başına ücret uygulanır.

* **Coğrafi Çoğaltma veri aktarımı maliyetleri**: Bu ücret, GRS ve RA-GRS dahil olmak üzere yalnızca coğrafi çoğaltma yapılandırılmış hesaplara uygulanır. Coğrafi çoğaltma veri aktarımı gigabayt başına ücret doğurur.

* **Giden veri aktarımı maliyetleri**: Giden veri aktarımları (bir Azure bölgesinin dışına aktarılan veriler), genel amaçlı depolama hesapları ile tutarlı şekilde gigabayt başına esaslı olarak bant genişliği kullanımı için fatura doğurur.

* **Depolama erişim katmanını değiştirme**: hesap depolama erişim katmanını seyrek erişimli iken sık erişimli olarak değiştirmek, depolama hesabında mevcut olan tüm verileri okumaya eşit bir ücret doğurur. Ancak, hesap erişim katmanını sık erişimli iken seyrek erişimli olarak değiştirmek, tüm verileri seyrek erişimli katmana (yalnızca GPv2 hesapları) yazmaya eşit bir ücret doğurur.

> [!NOTE]
> Depolama hesaplarına ilişkin fiyatlandırma modeli hakkında daha fazla bilgi için [Azure Depolama Fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/) sayfasına bakın. Giden veri aktarımı ücretlerine ilişkin daha fazla bilgi için [Veri Aktarımları Fiyatlandırma Bilgileri](https://azure.microsoft.com/pricing/details/data-transfers/) sayfasına bakın.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Geçerli kullanım desenleriniz için maliyetleri tahmin etme

Belirli bir katmandaki genel amaçlı v2 depolama hesabında blob verilerine depolama ve erişim maliyetini tahmin etmek için var olan kullanım modelinizi değerlendirin veya beklenen kullanım modelinizi yaklaşık olarak değerlendirin. Genel olarak, şunları bilmek istersiniz:

* BLOB depolama tüketiminize, gigabayt cinsinden şunlar dahildir:
  * Depolama hesabınızda ne kadar veri depolanıyor?
  * Aylık temelde veri hacmi nasıl değişiyor; yeni veriler sürekli eski verilerin yerini alıyor mu?

* BLOB depolama verilerinize yönelik birincil erişim deseninin yanı sıra:
  * Ne kadar veri okunmakta ve depolama hesabına yazıldı?
  * Depolama hesabındaki verilerde kaç okuma işlemi ve yazma işlemi meydana gelir?

Gereksinimlerinize uygun en iyi erişim katmanına karar vermek için, blob veri kapasitenizi ve bu verilerin nasıl kullanıldığını belirlemek yararlı olabilir. Bu, hesabınız için izleme ölçümlerine bakarak en iyi şekilde yapılabilir.

### <a name="monitoring-existing-storage-accounts"></a>Var olan depolama hesaplarını izleme

Var olan depolama hesaplarınızı izlemek ve bu verileri toplamak için, bir depolama hesabına yönelik günlüğe kaydetme işlemlerini gerçekleştiren ve ölçümler sağlayan Azure Depolama Analizi hizmetinden yararlanabilirsiniz. Depolama Analizi GPv1, GPv2 ve Blob depolama hesap türleri için toplu işlem istatistiklerini içeren ölçümleri ve depolama hizmetine yapılan isteklere ilişkin kapasite verilerini depolayabilir. Bu veriler aynı depolama hesabındaki iyi bilinen tablolara depolanır.

Daha fazla bilgi için bkz. [Storage Analytics Ölçümleri hakkında](../blobs/monitor-blob-storage.md) ve [Storage Analytics Ölçüm Tablosu Şeması](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema)

> [!NOTE]
> Blob depolama hesapları, Tablo hizmeti uç noktasını yalnızca ilgili hesabın ölçüm verilerini depolamak ve bunlara erişmek için kullanıma sunar.

Blob depolamada depolama tüketimini izlemek için kapasite ölçümlerini etkinleştirmeniz gerekir.
Bu etkinken, bir depolama hesabının blob hizmeti için kapasite verileri günlük olarak kaydedilir ve aynı depolama hesabındaki *$MetricsCapacityBlob* tablosuna yazılan bir tablo girişi olarak kaydedilir.

Blob depolama hizmetinin veri erişim desenlerini izlemek için API’den saatlik işlem ölçümlerini etkinleştirmeniz gerekir. Saatlik işlem ölçümleri etkinleştirildiğinde API başına işlemler saatte bir toplanır ve aynı depolama hesabındaki *$MetricsHourPrimaryTransactionsBlob* tablosuna yazılan bir tablo girişi olarak kaydedilir. RA-GRS depolama hesapları kullanılırken *$MetricsHourSecondaryTransactionsBlob* tablosu, işlemleri ikincil uç noktaya kaydeder.

> [!NOTE]
> Blok ve ekleme blobu verileriyle birlikte sayfa bloblarını ve sanal makine disklerini veya kuyrukları, dosyaları ya da tabloları depoladığınız genel amaçlı bir depolama hesabınız olması durumunda bu tahmin işlemi geçerli değildir. Kapasite verileri blok bloblarını diğer türlerden ayırt etmez ve diğer veri türleri için kapasite verileri sunmaz. Bu türleri kullanıyorsanız alternatif bir yöntem de en son faturanızdaki miktarlara bakmaktır.

Veri tüketim ve erişim modelinizi yaklaşık olarak tahmin etmek için, ölçümler için düzenli kullanımınızı temsil eden bir elde tutma süresi seçmeniz ve tahmin etmeniz önerilir. Seçeneklerden biri son yedi güne ait ölçüm verilerinin tutulması ve verilerin ay sonunda analiz için haftada bir toplanmasıdır. Diğer bir seçenek ise son 30 güne ait ölçüm verilerinin tutulması ve verilerin 30 günlük süre sonunda toplanıp çözümlenmesidir.

Ölçüm verilerini etkinleştirme, toplama ve görüntüleme hakkında daha fazla bilgi için bkz. [Storage Analytics ölçümleri](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Analiz verilerinin depolanması, erişimi ve indirilmesi de normal kullanıcı verileri gibi ücretlendirilir.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Kullanım ölçümlerinden yararlanarak maliyetleri tahmin etme

#### <a name="capacity-costs"></a>Kapasite maliyetleri

*$MetricsCapacityBlob* kapasite ölçüm tablosunda *'data'* satır anahtarını içeren en son giriş, kullanıcı verilerinin harcadığı kapasiteyi gösterir. *$MetricsCapacityBlob* kapasite ölçüm tablosunda *'analytics'* satır anahtarını içeren en son giriş, analiz günlüklerinin harcadığı kapasiteyi gösterir.

Hem kullanıcı verileri hem de analiz günlükleri (etkinse) tarafından kullanılan bu toplam kapasite, verileri depolama hesabına depolama maliyetini tahmin etmek için kullanılabilir. Aynı yöntem ayrıca GPv1 depolama hesaplarında depolanma maliyetlerini tahmin etmek için kullanılabilir.

#### <a name="transaction-costs"></a>İşlem maliyetleri

İşlem ölçüm tablosundaki bir API’nin tüm girişleri için *'TotalBillableRequests'* toplamı, ilgili API’nin toplam işlem sayısını belirtir. *Örneğin*, belirli bir süre içindeki *'GetBlob'* işlemlerinin toplam sayısı *'user;GetBlob'* satır anahtarını içeren tüm girişlere yönelik toplam faturalandırılabilir isteklerin toplamına göre hesaplanabilir.

Blob depolama hesaplarına ilişkin işlem maliyetlerini tahmin etmek için, farklı şekilde fiyatlandırıldıkları için işlemleri üç gruba ayırmanız gerekir.

* *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* ve *'CopyBlob'* gibi yazma işlemleri.
* *'DeleteBlob'* ve *'DeleteContainer'* gibi silme işlemleri.
* Diğer tüm işlemler.

GPv1 depolama hesaplarının işlem maliyetlerini tahmin etmek için işlemden/API’den bağımsız olarak tüm işlemleri toplamanız gerekir.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Veri erişimi ve coğrafi çoğaltma veri aktarımı maliyetleri

Storage Analytics bir depolama hesabından okunan ve depolama hesabına yazılan veri miktarını belirtmese de, işlem ölçümleri tablosuna bakılarak bu değer kabaca tahmin edilebilir. İşlem ölçüm tablosundaki bir API’nin tüm girişleri için *'TotalIngress'* toplamı, ilgili API’nin toplam giriş verileri miktarını bayt cinsinden belirtir. Benzer şekilde, *'TotalEgress'* toplamı toplam çıkış verileri miktarını bayt cinsinden belirtir.

Blob depolama hesaplarına ilişkin veri erişimi maliyetlerini hesaplamak için işlemleri iki gruba ayırmanız gerekir.

* Depolama hesabından alınan veri miktarı birincil olarak *'GetBlob'* ve *'CopyBlob'* işlemleri için *'TotalEgress'* toplamına bakılarak tahmin edilebilir.

* Depolama hesabına yazılan veri miktarı birincil olarak *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* ve *'AppendBlock'* işlemleri için *'TotalIngress'* toplamına bakılarak tahmin edilebilir.

Blob depolama hesaplarında coğrafi çoğaltma veri aktarımı maliyeti de bir GRS veya RA-GRS depolama hesabı kullanılırken yazılan veri miktarı tahmini kullanılarak hesaplanabilir.

> [!NOTE]
> Sık veya seyrek erişimli depolama erişim katmanını kullanma maliyetlerini hesaplama hakkında daha ayrıntılı bir örnek için, *' sık kullanılan ve seyrek erişimli erişim katmanları nelerdir ve hangisinin kullanılacağını nasıl belirlemeliyim? '* başlıklı SSS bölümüne göz atın. bkz. [Azure Depolama Fiyatlandırma Sayfası](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Sonraki adımlar

* [Depolama hesabına genel bakış](storage-account-overview.md)
* [Depolama hesabı oluşturma](storage-account-create.md)
* [Bir Azure Depolama hesabını başka bir bölgeye taşıma](storage-account-move.md)
* [Silinmiş bir depolama hesabını kurtarma](storage-account-recover.md)
