---
title: Gen1 'den Gen2 'e geçiş Azure Data Lake Storage
description: Azure Blob depolama üzerinde oluşturulan ve büyük veri analizlerine adanmış birtakım yetenekler sağlayan Gen1 'den Gen2 'e Azure Data Lake Storage geçirin.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 9d160738208bcef7b066567137a3c3a9738c26a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727017"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Gen1 'den Gen2 'e geçiş Azure Data Lake Storage

Verilerinizi, iş yüklerinizi ve uygulamalarınızı Data Lake Storage 2. Data Lake Storage 1. geçirebilirsiniz.

Azure Data Lake Storage 2., [Azure Blob depolamada](storage-blobs-introduction.md) oluşturulmuştur ve büyük veri analizlerine adanmış bir dizi özellik sağlar. [Data Lake Storage 2.](https://azure.microsoft.com/services/storage/data-lake-storage/) , [Azure Blob depolamadan](storage-blobs-introduction.md)düşük maliyetli, katmanlı depolama, yüksek kullanılabilirlik/olağanüstü durum kurtarma özellikleriyle ölçeklendirerek, dosya sistemi semantiği, dizin ve dosya düzeyi güvenliği gibi [Azure Data Lake Storage 1.](../../data-lake-store/index.yml)özelliklerini birleştirir.

> [!NOTE]
> Daha kolay okunması için bu makalede, Azure Data Lake Storage 1. başvurmak için *Gen1* terimini ve Azure Data Lake Storage 2. başvurmak için *Gen2* terimi kullanılmaktadır.

## <a name="recommended-approach"></a>Önerilen yaklaşım

Gen2 'e geçiş yapmak için aşağıdaki yaklaşımı öneririz.

: heavy_check_mark: adım 1: hazırlığı değerlendir

: heavy_check_mark: adım 2: geçirmeye hazırlanma

: heavy_check_mark: 3. Adım: veri ve uygulama iş yüklerini geçirme

: heavy_check_mark: adım 4: cutover from Gen1 to Gen2

> [!NOTE]
> Gen1 ve Gen2 farklı hizmetlerdir; yerinde yükseltme deneyimi yoktur ve bilerek geçiş çabaları gereklidir. 

### <a name="step-1-assess-readiness"></a>1. Adım: hazırlığı değerlendir

1. [Data Lake Storage 2. sunumu](https://azure.microsoft.com/services/storage/data-lake-storage/)hakkında bilgi edinin; avantaj, maliyetler ve genel mimaridir. 

2. Gen1 yeteneklerini Gen2 ile [karşılaştırın](#gen1-gen2-feature-comparison) . 

3. İşlevsellikten herhangi bir boşlukları değerlendirmek için [bilinen sorunların](data-lake-storage-known-issues.md) bir listesini gözden geçirin.

4. Gen2, [tanılama günlüğü](../common/storage-analytics-logging.md), [erişim katmanları](storage-blob-storage-tiers.md)ve [BLOB depolama yaşam döngüsü yönetim ilkeleri](storage-lifecycle-management-concepts.md)gibi BLOB depolama özelliklerini destekler. Bu özelliklerden herhangi birini kullanarak ilginç değilseniz, [geçerli destek düzeyini](./data-lake-storage-supported-blob-storage-features.md)gözden geçirin.

5. Gen2 'in çözümlerinizin bağımlı olduğu tüm hizmetleri desteklediğinden emin olmak için [Azure ekosistem desteğinin](./data-lake-storage-multi-protocol-access.md) güncel durumunu gözden geçirin.

### <a name="step-2-prepare-to-migrate"></a>2. Adım: geçirmeye hazırlanma

1. Geçirilecek veri kümelerini belirler.

   Artık kullanılmayan veri kümelerini temizlemek için bu fırsatı kullanın. Tüm verilerinizi tek seferde geçirmeyi planlamıyorsanız, aşamalar halinde geçirebileceğiniz mantıksal veri gruplarını belirlemek için şu zamana kadar yararlanın.
   
2. Bir geçişin işinizde sahip olacağı etkiyi belirleme.

   Örneğin, geçiş gerçekleşirken kapalı kalma süresi olup olmadığını göz önünde bulundurun. Bu konular uygun bir geçiş modelini belirlemenize ve en uygun araçları seçmenize yardımcı olabilir.

3. Bir geçiş planı oluşturun. 

   Bu [geçiş düzenlerini](#migration-patterns)öneririz. Bu desenlerden birini seçebilir, bunları birlikte birleştirebilir veya kendi özel desenini tasarlayabilirsiniz.

### <a name="step-3-migrate-data-workloads-and-applications"></a>3. Adım: verileri, iş yüklerini ve uygulamaları geçirme

Tercih ettiğiniz kalıbı kullanarak verileri, iş yüklerini ve uygulamaları geçirin. Senaryoları artımlı olarak doğrulamanızı öneririz.

1. [Bir depolama hesabı oluşturun](../common/storage-account-create.md) ve hiyerarşik ad alanı özelliğini etkinleştirin. 

2. Verilerinizi geçirin. 

3. Gen2 uç noktanıza işaret etmek için [iş yüklerinizde Hizmetleri](./data-lake-storage-supported-azure-services.md) yapılandırın. 
   
4. Uygulamaları Gen2 API 'Lerini kullanacak şekilde güncelleştirin. Bkz. [.net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) ve [rest](/rest/api/storageservices/data-lake-storage-gen2)için kılavuzlar. 
   
5. Data Lake Storage 2. [PowerShell cmdlet 'leri](data-lake-storage-directory-file-acl-powershell.md)ve [Azure CLI komutlarını](data-lake-storage-directory-file-acl-cli.md)kullanmak için betikleri güncelleştirin.
   
6. `adl://`Kod dosyalarında dize veya Databricks Not defterleri, Apache Hive HQL dosyaları ya da iş yüklerinizin bir parçası olarak kullanılan başka bir dosya IÇEREN URI başvurularını arayın. Bu başvuruları, yeni depolama hesabınızın [Gen2 BIÇIMLI URI](data-lake-storage-introduction-abfs-uri.md) 'siyle değiştirin. Örneğin: Gen1 URI: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` olabilir `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile` . 

7. [Azure rolleri](../common/storage-auth-aad-rbac-portal.md), [dosya ve klasör düzeyinde güvenlik](data-lake-storage-access-control.md)ve [Azure Storage güvenlik duvarları ve sanal ağlar](../common/storage-network-security.md)dahil olmak üzere hesabınızdaki güvenliği yapılandırın.

### <a name="step-4-cutover-from-gen1-to-gen2"></a>4. Adım: Gen1 'den Gen2 'ye cutover

Uygulamalarınızın ve iş yüklerinizin Gen2 üzerinde kararlı olduğundan emin olduktan sonra, iş senaryolarınızı karşılamak için Gen2 kullanmaya başlayabilirsiniz. Gen1 üzerinde çalışan ve Gen1 hesabınızın yetkisini alan kalan tüm işlem hatlarını kapatın. 

<a id="gen1-gen2-feature-comparison"></a>

## <a name="gen1-vs-gen2-capabilities"></a>Gen1 vs Gen2 özellikleri

Bu tablo, Gen1 'in yeteneklerini Gen2 ile karşılaştırır.

|Alan |Gen1   |Gen2 |
|---|---|---|
|Veri organizasyonu|[Hiyerarşik ad alanı](data-lake-storage-namespace.md)<br>Dosya ve klasör desteği|[Hiyerarşik ad alanı](data-lake-storage-namespace.md)<br>Kapsayıcı, dosya ve klasör desteği |
|Coğrafi yedeklilik| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Kimlik Doğrulaması|[AAD yönetilen kimliği](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Hizmet sorumluları](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD yönetilen kimliği](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Hizmet sorumluları](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Paylaşılan erişim anahtarı](/rest/api/storageservices/authorize-with-shared-key)|
|Yetkilendirme|Yönetim- [Azure RBAC](../../role-based-access-control/overview.md)<br>Veri – [ACL 'ler](data-lake-storage-access-control.md)|Yönetim – [Azure RBAC](../../role-based-access-control/overview.md)<br>Veri  [ACL 'leri](data-lake-storage-access-control.md), [Azure RBAC](../../role-based-access-control/overview.md) |
|Şifreleme – bekleyen veriler|Sunucu tarafı – [Microsoft tarafından yönetilen](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) veya [müşteri tarafından yönetilen](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) anahtarlarla|Sunucu tarafı – [Microsoft tarafından yönetilen](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) veya [müşteri tarafından yönetilen](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) anahtarlarla|
|VNET desteği|[Sanal Ağ Tümleştirmesi](../../data-lake-store/data-lake-store-network-security.md)|[Hizmet uç noktaları](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Özel uç noktalar](../common/storage-private-endpoints.md)|
|Geliştirici deneyimi|[Rest](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.net](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|Genel olarak kullanılabilir- [rest](/rest/api/storageservices/data-lake-storage-gen2), [.net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)<br>Genel Önizleme- [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [POWERSHELL](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Kaynak günlükleri|Klasik Günlükler<br>[Azure Izleyici tümleşik](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Klasik Günlükler](../common/storage-analytics-logging.md) -genel kullanıma sunuldu<br>[Azure izleyici tümleşik](monitor-blob-storage.md) – Önizleme|
|Ekosistem|[HDInsight (3,6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3,1 ve üzeri)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [Azure SYNAPSE Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3,6, 4,0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5,1 ve üzeri)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [Azure SYNAPSE Analytics](../../azure-sql/database/vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns"></a>

## <a name="gen1-to-gen2-patterns"></a>Gen1-Gen2 desenleri

Bir geçiş kalıbı seçin ve ardından bu kalıbı gerektiği gibi değiştirin.

|Geçiş deseninin | Ayrıntılar |
|---|---|
|**Yükselt ve Kaydır**|En basit model. Veri işlem hatlarınız için kapalı kalma süresi uygun değilse idealdir.|
|**Artımlı kopya**|*Asansör ve kaydırma* gibi, ancak daha az kapalı kalma süresine benzer. Kopyalamanın uzun süredeki büyük miktarlarda veri için idealdir.|
|**Çift işlem hattı**|Herhangi bir kesinti süresi karşılayamıyorum işlem hatları için idealdir.|
|**Çift yönlü eşitleme**|*Çift ardışık düzene* benzer, ancak daha karmaşık işlem hatları için uygun olan daha aşamalı bir yaklaşım ile.|

Her bir düzene daha yakından göz atalım.
 
### <a name="lift-and-shift-pattern"></a>Yükselt ve kaydırma stili

Bu en basit modeldir. 

1. Tüm Gen1 yazma işlemlerini durdur.

2. Verileri Gen1 'ten Gen2 'e taşıyın. [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md)öneririz. ACL 'Leri verilerle kopyalayın.

3. Gen2 'e giriş işlemlerini ve iş yüklerini işaret edin.

4. Gen1 yetkisini alma.

[Asansör ve kaydırma geçiş](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Lift%20and%20Shift/README.md)örneğimizde bulunan Asansör ve kaydırma deseninin örnek kodumuza göz atın.

> [!div class="mx-imgBorder"]
> ![yükselt ve kaydırma stili](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Asansör ve kaydırma deseninin kullanımı hakkında konular

: heavy_check_mark: aynı anda tüm iş yükleri için Gen1 ile Gen2 arasında cutover.

: heavy_check_mark: geçiş sırasında kapalı kalma süresi ve tam geçişi dönemi beklenir.

: heavy_check_mark: kapalı kalma süresi ve tüm uygulamalar tek seferde yükseltilemeyen işlem hatları için Idealdir.

### <a name="incremental-copy-pattern"></a>Artımlı kopyalama kalıbı

1. Gen1 'den Gen2 'e veri taşımaya başlayın. [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md)öneririz. ACL 'Leri verilerle kopyalayın.

2. Yeni verileri artımlı olarak Gen1 'tan kopyalayın.

3. Tüm veriler kopyalandıktan sonra, tüm Gen1 yazma işlemlerini durdurun ve iş yüklerini Gen2 'e işaret edin.

4. Gen1 yetkisini alma.

[Artımlı kopyalama geçiş](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Incremental/README.md)örneğimizde artımlı kopyalama deseninin örnek kodumuza göz atın.


> [!div class="mx-imgBorder"]
> ![Artımlı kopyalama kalıbı](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Artımlı kopyalama düzeninin kullanılmasıyla ilgili konular:

: heavy_check_mark: aynı anda tüm iş yükleri için Gen1 ile Gen2 arasında cutover.

: heavy_check_mark: yalnızca tam geçişi dönemi sırasında kapalı kalma süresi beklenir.

: heavy_check_mark: tüm uygulamaların tek seferde yükseltildiği, ancak veri kopyasının daha fazla zaman gerektirmesi halinde işlem hatları için Idealdir.

### <a name="dual-pipeline-pattern"></a>Çift ardışık düzen düzeni

1. Verileri Gen1 'ten Gen2 'e taşıyın. [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md)öneririz. ACL 'Leri verilerle kopyalayın.

2. Hem Gen1 hem de Gen2 'e yeni veri alma.

3. Gen2 için iş yüklerini işaret edin.

4. Tüm Gen1 yazma işlemlerini durdurun ve ardından Gen1 yetkisini alın.

[Çift ardışık düzen geçiş örneğimizde](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Dual%20pipeline/README.md)çift ardışık düzen deseninin örnek kodumuzu inceleyin.

> [!div class="mx-imgBorder"]
> ![Çift ardışık düzen düzeni](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Çift ardışık düzen deseninin kullanılmasıyla ilgili konular:

: heavy_check_mark: gen1 ve Gen2 işlem hatları yan yana çalışır.

: heavy_check_mark: sıfır kapalı kalma süresini destekler.

: heavy_check_mark: iş yüklerinizin ve uygulamalarınızın herhangi bir kesinti yaşanmadığı durumlarda Idealdir ve her iki depolama hesabına de sahip olabilirsiniz.

### <a name="bi-directional-sync-pattern"></a>İki yönlü eşitleme kalıbı

1. Gen1 ve Gen2 arasında çift yönlü çoğaltmayı ayarlayın. [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)öneririz. Mevcut veriler için bir onarım özelliği sunar.

3. Tüm taşımalar tamamlandığında, tüm Gen1 yazma işlemlerini durdurun ve çift yönlü çoğaltmayı devre dışı bırakın.

4. Gen1 yetkisini alma.

Çift yönlü eşitleme [geçiş](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Bi-directional/README.md)örneğimizde çift yönlü eşitleme deseninin örnek kodumuzu inceleyin.

> [!div class="mx-imgBorder"]
> ![Çift yönlü desenler](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>İki yönlü eşitleme deseninin kullanılmasıyla ilgili konular:

: heavy_check_mark: aşamalı bir yaklaşımın daha anlamlı olabileceği çok sayıda işlem hattı ve bağımlılığı içeren karmaşık senaryolar için Idealdir.  

: heavy_check_mark: geçiş çabaları yüksektir, ancak gen1 ve Gen2 için yan yana destek sağlamaktadır.

## <a name="next-steps"></a>Sonraki adımlar

- Bir depolama hesabı için güvenliği ayarlamanın çeşitli bölümleri hakkında bilgi edinin. Bkz. [Azure Storage Güvenlik Kılavuzu](./security-recommendations.md).
- Data Lake Store performansını iyileştirin. Bkz. [performans için Azure Data Lake Storage 2. iyileştirme](data-lake-storage-performance-tuning-guidance.md)
- Data Lake Store yönetmek için en iyi uygulamaları gözden geçirin. Bkz. [Azure Data Lake Storage 2. kullanımı Için en iyi uygulamalar](data-lake-storage-best-practices.md)