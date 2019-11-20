---
title: Azure SYNAPSE Analytics için bir sayfa sayfası (eski adıyla SQL DW)
description: Azure SYNAPSE Analytics (eski adıyla SQL DW) çözümlerinizi hızlıca oluşturmak için bağlantıları ve en iyi uygulamaları bulun.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9355ae1522c653924574b94594e894fdaf3f764e
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646659"
---
# <a name="cheat-sheet-for-azure-synapse-analytics-formerly-sql-dw"></a>Azure SYNAPSE Analytics için bir sayfa sayfası (eski adıyla SQL DW)

Bu görsel sayfa, Azure SYNAPSE çözümleri oluşturmak için yardımcı ipuçları ve en iyi uygulamalar sağlar. 

Aşağıdaki grafikte, veri ambarı tasarlama işlemi gösterilmektedir:

![Taslak]

## <a name="queries-and-operations-across-tables"></a>Tablolardaki sorgular ve işlemler

Veri ambarınızda çalıştırılacak birincil işlemleri ve sorguları önceden bildiğinizde, bu işlemler için veri ambarı mimarinizi öncelik sırasına koyabilirsiniz. Bu sorgular ve işlemler şunları içerebilir:
* Bir veya iki olgu tablosunu boyut tabloları ile birleştirme, birleşik tabloyu filtreleme ve sonra sonuçları bir veri reyonuna ekleme.
* Olgu satışınıza yönelik büyük veya küçük güncelleştirmeler yapma.
* Tablolarınıza yalnızca veri ekleme.

İşlemlerin türlerini önceden bilmeniz, tablolarınızın tasarımını iyileştirmenize yardımcı olur.

## <a name="data-migration"></a>Veri geçişi

İlk olarak, verilerinizi [Azure Data Lake Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store) veya Azure Blob depolama alanına yükleyin. Daha sonra, PolyBase kullanarak verilerinizi hazırlama tablolarına yükleyin. Aşağıdaki yapılandırmayı kullanın:

| Tasarım | Öneri |
|:--- |:--- |
| Dağıtım | Hepsini Bir Kez Deneme |
| Dizinleme | Yığın |
| Bölümleme | None |
| Kaynak Sınıfı | largerc veya xlargerc |

[Veri geçişi], [veri yükleme] ve [Ayıklama, Yükleme ve Dönüştürme (ELT) işlemi](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) hakkında daha fazla bilgi edinin. 

## <a name="distributed-or-replicated-tables"></a>Dağıtılmış veya çoğaltılmış tablolar

Tablo özelliklerini bağlı olarak aşağıdaki stratejileri kullanın:

| Tür | İdeal olduğu öğe...| İzlenmesi gereken şey...|
|:--- |:--- |:--- |
| Çoğaltılmış | • Sıkıştırmadan (~5x sıkıştırma) sonra 2 GB’tan az depolama alanına sahip bir yıldız şemasındaki küçük boyut tabloları |• Tabloda birçok yazma işlemi olup olmadığı (örn. ekleme, upsert, silme, güncelleştirme)<br></br>• Veri Ambarı Birimleri (DWU) sağlamayı sık sık değiştirip değiştirmediğiniz<br></br>• Yalnızca 2-3 sütun kullanıp kullanmadığınız, ancak tablonuzun birçok sütun içerip içermediği<br></br>• Çoğaltılmış bir tabloyu dizinleyip dizinlemediğiniz |
| Hepsini Bir Kez Deneme (varsayılan) | • Geçici/hazırlama tablosu<br></br> • Belirgin bir birleştirme anahtarı veya iyi aday sütunu olmaması |• Veri taşıma nedeniyle performansın düşüp düşmediği |
| Karma | • Olgu tabloları<br></br>• Büyük boyut tabloları |• Dağıtım anahtarının güncelleştirilip güncelleştirilemediği |

**İpuçları:**
* Hepsini Bir Kez Deneme ile başlayın, ancak yüksek düzeyde paralel bir mimariden yararlanmak için karma dağıtım stratejisini amaçlayın.
* Genel karma anahtarların aynı veri biçimine sahip olduğundan emin olun.
* Varchar biçiminde dağıtmayın.
* Sık birleştirme işlemleri ile bir olgu tablosuna yönelik genel karma anahtar içeren boyut tabloları karma dağıtılmış olabilir.
* Verilerdeki eğrilikleri analiz etmek için *[sys.dm_pdw_nodes_db_partition_stats]* komutunu kullanın.
* Sorguların ardındaki veri hareketlerini analiz etmek, yayın ve karıştırma işlemlerinin aldığı süreyi izlemek için *[sys.dm_pdw_request_steps]* komutunu kullanın. Bu, dağıtım stratejinizi gözden geçirmek için faydalıdır.

[Çoğaltılmış tablolar] ve [dağıtılmış tablolar] hakkında daha fazla bilgi edinin.

## <a name="index-your-table"></a>Tablonuzu dizinleme

Dizinleme, tabloların hızlı şekilde okunması için faydalıdır. Gereksinimlerinize göre kullanabileceğiniz bir dizi benzersiz teknoloji vardır:

| Tür | İdeal olduğu öğe... | İzlenmesi gereken şey...|
|:--- |:--- |:--- |
| Yığın | • Hazırlama/geçici tablo<br></br>• Küçük aramalar içeren küçük tablolar |• Aramaların tam tabloyu tarayıp taramadığı |
| Kümelenmiş dizin | • En fazla 100 milyon satır içeren tablolar<br></br>• Yalnızca 1-2 sütunun yoğun şekilde kullanıldığı büyük tablolar (100 milyondan fazla satır içeren) |• Çoğaltılmış bir tabloda kullanılıp kullanılmadığı<br></br>• Birden fazla birleştirme ve Gruplama Ölçütü işlemini içeren karmaşık sorgularınız olup olmadığı<br></br>• Dizinlenmiş sütunlarda güncelleştirme yapıp yapmadığınız: bu, bellek tüketir |
| Kümelenmiş columnstore dizini (CCI) (varsayılan) | • Büyük tablolar (100 milyondan fazla satır) | • Çoğaltılmış bir tabloda kullanılıp kullanılmadığı<br></br>• Tablonuzda yoğun güncelleştirme işlemleri yapıp yapmadığınız<br></br>• Tablonuzu aşırı bölümleyip bölümlemediğiniz: satır grupları, farklı dağıtım düğümlerine ve bölümlerine yayılmaz |

**İpuçları:**
* Kümelenmiş bir dizin üzerinde, filtreleme için yoğun şekilde kullanılan bir sütuna kümelenmemiş bir dizin eklemek isteyebilirsiniz. 
* CCI ile bir tablodaki belleği nasıl yöneteceğiniz konusunda dikkatli olun. Veri yüklediğinizde, kullanıcının (veya sorgunun) büyük bir kaynak sınıfından avantaj elde etmesini istersiniz. Kırpmadan ve çok sayıda küçük sıkıştırılmış satır grupları oluşturmaktan kaçınmaya dikkat edin.
* Gen2’de, CCI tabloları performansı en üst düzeye çıkarmak için işlem düğümlerinde yerel olarak önbelleğe alınır.
* CCI için, satır gruplarınızın sıkıştırmasının zayıf olması nedeniyle düşük performans gerçekleşebilir. Bu durumda CCI’nizi yeniden derleyin veya yeniden düzenleyin. Sıkıştırılmış satır grubu başına en az 100.000 satır istiyorsunuz. Bir satır grubunda 1 milyon satır olması idealdir.
* Artımlı yük sıklığı ve boyutuna bağlı olarak, dizinlerinizi yeniden düzenlerken veya yeniden derlerken otomatikleştirme sağlamak istersiniz. Bahar temizliği her zaman yararlıdır.
* Bir satır grubunun kırpmak istediğinizde stratejik olun. Açık satır grupları ne kadar büyüktür? Yaklaşan günlerde ne kadar veri yüklemeyi bekliyorsunuz?

[Dizinler] hakkında daha fazla bilgi edinin.

## <a name="partitioning"></a>Bölümleme
Büyük bir olgu tablonuz (1 milyardan fazla satır) olduğunda tablonuzu bölümleyebilirsiniz. Durumların yüzde 99’unda bölüm anahtarı, tarihi temel almalıdır. Özellikle de kümelenmiş bir columnstore dizininiz olduğunda aşırı bölümleme yapmamaya dikkat edin.

ELT gerektiren hazırlama tabloları ile bölümlemeden yararlanabilirsiniz. Bu, veri yaşam döngüsü yönetimini kolaylaştırır.
Özellikle kümelenmiş bir columnstore dizininde verilerinizi aşırı bölümlememeye dikkat edin.

[Bölümler] hakkında daha fazla bilgi edinin.

## <a name="incremental-load"></a>Artımlı yükleme

Verilerinizi artımlı olarak yükleyecekseniz ilk olarak verilerinizi yüklemeye daha büyük kaynak sınıfları ayırdığınızdan emin olun.  Bu, kümelenmiş columnstore dizinleri olan tablolara yüklenirken özellikle önemlidir.  Daha fazla ayrıntı için bkz. [kaynak sınıfları](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management) .  

ELT işlem hatlarınızı veri ambarınıza otomatik hale getirmek için PolyBase ve ADF v2 kullanmanızı öneririz.

Geçmiş verilerinizde oluşan büyük toplu güncelleştirmeler için, INSERT, UPDATE ve DELETE kullanmak yerine bir tabloda tutmak istediğiniz verileri yazmak üzere bir [CTAS](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) kullanmayı düşünün.

## <a name="maintain-statistics"></a>İstatistiklerin bakımını yapın
 Otomatik istatistikler genel kullanıma sunulduğunda, istatistiklerin el ile Bakımı gereklidir. Verilerinizde *önemli* değişiklikler olacağından, istatistiklerin güncelleştirilmesi önemlidir. Bu, sorgu planlarınızın iyileştirilmesine yardımcı olur. Tüm istatistiklerinizi tutmanız çok uzun sürerse, hangi sütunlarda istatistikler olduğu konusunda daha seçici olun. 

Güncelleştirmelerin sıklığını da tanımlayabilirsiniz. Örneğin, yeni değerlerin eklenme ihtimali olan tarih sütunlarını her gün güncelleştirmeyi tercih edebilirsiniz. En çok faydayı, birleştirmelerin bulunduğu sütunlar, WHERE yan tümcesinde kullanılan sütunlar ve GROUP BY içinde bulunan sütunlar için istatistik tutarak elde edebilirsiniz.

[İstatistikler] hakkında daha fazla bilgi edinin.

## <a name="resource-class"></a>Kaynak sınıfı
Kaynak grupları, sorgulara bellek ayırmak için bir yol olarak kullanılır. Sorgu veya yükleme hızını artırmak için daha fazla bellek gerekiyorsa, daha yüksek kaynak sınıfları ayırmanız gerekir. Çevirme tarafında büyük kaynak sınıfları kullanılması, eşzamanlılığı etkiler. Tüm kullanıcılarınızı büyük bir kaynak sınıfına taşımadan önce bunu dikkate almak istersiniz.

Sorguların çok uzun sürdüğünü fark ederseniz, kullanıcılarınızın büyük kaynak sınıflarında çalışmadığından emin olun. Büyük kaynak sınıfları birçok eşzamanlı yuva kullanır. Bunlar diğer sorguların kuyruğa alınmasına neden olabilir.

Son olarak, [SQL havuzu](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse)Gen2 kullanarak her kaynak sınıfı, Gen1 'den daha fazla bellek 2,5 alır.

[Kaynak sınıfları ve eşzamanlılık] ile çalışma hakkında daha fazla bilgi edinin.

## <a name="lower-your-cost"></a>Maliyetinizi düşürme
Azure SYNAPSE 'ın temel bir özelliği, [işlem kaynaklarını yönetme](sql-data-warehouse-manage-compute-overview.md)olanağıdır. SQL havuzunu kullanmadığınız sırada duraklatabilirsiniz, bu da işlem kaynaklarının faturalandırmasını sonlandırır. Performans taleplerinizi karşılamak için kaynakları ölçeklendirebilirsiniz. Duraklatmak için [Azure portalını](pause-and-resume-compute-portal.md) veya [PowerShell](pause-and-resume-compute-powershell.md)’i kullanın. Ölçeklendirmek için [Azure portalını](quickstart-scale-compute-portal.md), [Powershell](quickstart-scale-compute-powershell.md)’i, [T-SQL](quickstart-scale-compute-tsql.md)’i veya [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute)’sini kullanın.

Azure İşlevleri ile istediğiniz anda otomatik ölçeklendirme yapın:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Performans için mimarinizi iyileştirme

Hub ve bağlı bileşen mimarisindeki SQL Database ve Azure Analysis Services’in dikkate alınmasını öneririz. Bu çözüm, bir yandan SQL Database ve Azure Analysis Services’teki gelişmiş güvenlik özelliklerini kullanırken diğer yandan farklı kullanıcı grupları arasında iş yükü yalıtımı sağlayabilir. Bu, kullanıcılarınıza sınırsız eşzamanlılık sağlamanın da bir yoludur.

[Azure SYNAPSE 'ın avantajlarından yararlanan tipik mimariler](https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/)hakkında daha fazla bilgi edinin.

SQL havuzundaki SQL veritabanlarındaki bağlı uçlarınıza tek bir tıklama ile dağıtın:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[Taslak]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[veri yükleme]:design-elt-data-loading.md
[deeper guidance]:guidance-for-loading-data.md
[dizinler]:sql-data-warehouse-tables-index.md
[bölümler]:sql-data-warehouse-tables-partition.md
[istatistikler]:sql-data-warehouse-tables-statistics.md
[Kaynak sınıfları ve eşzamanlılık]:resource-classes-for-workload-management.md
[çoğaltılmış tablolar]:design-guidance-for-replicated-tables.md
[dağıtılmış tablolar]:sql-data-warehouse-tables-distribute.md

<!--MSDN references-->


<!--Other Web references-->
[typical architectures that take advantage of SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/
[is and is not]:https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[veri geçişi]: https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/

[Azure Data Lake Storage]: ../data-factory/connector-azure-data-lake-store.md
[sys.dm_pdw_nodes_db_partition_stats]: /sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
