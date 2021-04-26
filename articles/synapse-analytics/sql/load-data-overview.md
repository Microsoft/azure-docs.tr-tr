---
title: Adanmış SQL havuzu için PolyBase veri yükleme stratejisi tasarlama
description: ETL yerine, adanmış SQL ile veri yüklemek için bir ayıklama, yükleme ve dönüştürme (ELT) işlemi tasarlayın.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 518843e688da7f940b36e77aee2667b4984ea5a3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567372"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzu için PolyBase veri yükleme stratejisi tasarlama

Geleneksel SMP veri ambarları, verileri yüklemek için bir ayıklama, dönüştürme ve yükleme (ETL) işlemi kullanır. Azure SQL havuzu, bilgi işlem ve depolama kaynaklarının ölçeklenebilirlik ve esnekliğinden faydalanan, yüksek düzeyde paralel işleme (MPP) mimarisidir. Ayıklama, yükleme ve dönüştürme (ELT) işleminin kullanılması, yerleşik olarak dağıtılan sorgu işleme özelliğinden yararlanabilir ve yüklemeden önce verileri dönüştürmek için gereken kaynakları ortadan kaldırabilir.

SQL havuzu BCP ve SQL BulkCopy API gibi PolyBase seçenekleri de dahil olmak üzere çok sayıda yükleme yöntemini destekleirken, yükleme tarihinin en hızlı ve en ölçeklenebilir yolu PolyBase aracılığıyla yapılır.  PolyBase, Azure Blob depolamada depolanan dış verilere veya T-SQL dili üzerinden Azure Data Lake Store erişen bir teknolojidir.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Ayıklama, yükleme ve dönüştürme (ELT)

Ayıklama, yükleme ve dönüştürme (ELT), verilerin bir kaynak sistemden ayıklandığı, bir veri ambarına yüklendiği ve sonra dönüştürülebileceği bir işlemdir.

Adanmış SQL havuzu için PolyBase ELT uygulamaya yönelik temel adımlar şunlardır:

1. Kaynak verileri metin dosyalarına ayıklayın.
2. Verileri Azure Blob depolama alanına veya Azure Data Lake Store ekleyin.
3. Verileri yükleme için hazırlayın.
4. PolyBase kullanarak verileri adanmış SQL havuzu hazırlama tablolarına yükleyin.
5. Verileri dönüştürün.
6. Verileri üretim tablolarına ekleyin.

Yükleme öğreticisi için bkz. [Azure Blob depolama 'Dan Azure SYNAPSE Analytics 'e veri yüklemek Için PolyBase kullanma](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

Daha fazla bilgi için bkz. [desenleri web günlüğü yükleme](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies).

## <a name="1-extract-the-source-data-into-text-files"></a>1. kaynak verileri metin dosyalarına ayıklayın

Kaynak sisteminizden veri alma, depolama konumuna bağlıdır.  Amaç, verileri PolyBase tarafından desteklenen sınırlandırılmış metin dosyalarına taşımaktır.

### <a name="polybase-external-file-formats"></a>PolyBase dış dosya biçimleri

PolyBase UTF-8 ve UTF-16 kodlamalı sınırlandırılmış metin dosyalarından veri yükler. Sınırlandırılmış metin dosyalarına ek olarak, bu dosya, RC dosyası, ORC ve Parquet Hadoop dosya biçimlerini yükler. PolyBase, gzip ve Snappy sıkıştırılmış dosyalarındaki verileri de yükleyebilir. PolyBase Şu anda genişletilmiş ASCII, sabit genişlikli biçim ve WinZip, JSON ve XML gibi iç içe geçmiş biçimleri desteklemez.

SQL Server dışarı aktarıyorsanız, verileri sınırlandırılmış metin dosyalarına aktarmak için [bcp komut satırı aracını](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) kullanabilirsiniz. Parquet, Azure SYNAPSE Analytics veri türü eşlemesine aşağıdaki gibidir:

| **Parquet veri türü** |                      **SQL veri türü**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            etmenize                             |
|        double         |                          küçük para                          |
|        string         |                            nchar                             |
|        string         |                           nvarchar                           |
|        string         |                             char                             |
|        string         |                           varchar                            |
|        ikili         |                            ikili                            |
|        ikili         |                          ikili                           |
|       timestamp       |                             date                             |
|       timestamp       |                        girişin                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           datetime                           |
|       timestamp       |                             time                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. verileri Azure Blob depolama alanına veya Azure Data Lake Store ekleyin

Verileri Azure depolama 'ya taşımak için [Azure Blob depolama](../../storage/blobs/storage-blobs-introduction.md) alanına veya [Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)taşıyabilirsiniz. Her iki konumda da veriler metin dosyalarında depolanmalıdır. PolyBase her iki konumdan de yüklenebilir.

Verileri Azure depolama 'ya taşımak için kullanabileceğiniz araçlar ve hizmetler:

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) hizmeti ağ aktarım hızını, performansı ve öngörülebilirlik geliştirir. ExpressRoute, verilerinizi Azure 'a adanmış bir özel bağlantıyla yönlendiren bir hizmettir. ExpressRoute bağlantıları, verileri genel İnternet üzerinden yönlendirmez. Bağlantılar, genel İnternet üzerinden tipik bağlantılardan daha fazla güvenilirlik, daha hızlı hız, daha düşük gecikme süreleri ve daha yüksek güvenlik sunar.
- [AZCopy yardımcı programı](../../storage/common/storage-use-azcopy-v10.md) , verileri genel Internet üzerinden Azure depolama 'ya taşıtabilecek. Bu, veri boyutlarınızın 10 TB 'den küçük olması durumunda geçerlidir. AZCopy ile düzenli olarak yükleme gerçekleştirmek için, kabul edilebilir olup olmadığını görmek için ağ hızını test edin.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , yerel sunucunuza yükleyebileceğiniz bir ağ geçidine sahip olabilir. Daha sonra, yerel sunucunuzdaki verileri Azure depolama 'ya taşımak için bir işlem hattı oluşturabilirsiniz. Adanmış SQL havuzu ile Data Factory kullanmak için bkz. [verileri ADANMıŞ SQL havuzuna yükleme](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. verileri yükleme için hazırlama

Adanmış SQL havuzuna yüklemeden önce Depolama hesabınızdaki verileri hazırlamanız ve temizlemeniz gerekebilir. Veriler kaynakta olduğunda veri hazırlama gerçekleştirilebilir, verileri metin dosyalarına dışarı aktardığınızda veya veriler Azure Storage 'dayken olur.  Mümkün olduğunca önce verilerle çalışmak en kolay yoldur.  

### <a name="define-external-tables"></a>Dış tabloları tanımlama

Veri yükleyebilmeniz için önce veri Ambarınızda dış tablolar tanımlamanız gerekir. PolyBase, Azure depolama 'daki verileri tanımlamak ve verilere erişmek için dış tabloları kullanır. Dış tablo, veritabanı görünümüne benzer. Dış tablo tablo şemasını içerir ve veri ambarının dışında depolanan verileri gösterir.

Dış tabloları tanımlama, veri kaynağını, metin dosyalarının biçimini ve tablo tanımlarını belirtmeyi içerir. Aşağıdakiler, gereken T-SQL sözdizimi konularından aşağıda verilmiştir:

- [DıŞ VERI KAYNAĞı OLUŞTUR](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [DıŞ TABLO OLUŞTUR](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>Metin dosyalarını biçimlendirme

Dış nesneler tanımlandıktan sonra, metin dosyalarının satırlarını dış tablo ve dosya biçimi tanımıyla hizalamanız gerekir. Metin dosyasının her satırındaki veriler tablo tanımıyla hizalanmalıdır.
Metin dosyalarını biçimlendirmek için:

- Verileriniz ilişkisel olmayan bir kaynaktan geliyorsa, onu satırlara ve sütunlara dönüştürmeniz gerekir. Verilerin ilişkisel veya ilişkisel olmayan bir kaynaktan olup olmadığı, verileri yüklemeyi planladığınız tablonun sütun tanımlarına göre hizalanacak şekilde dönüştürülmesi gerekir.
- SQL havuzu hedef tablosundaki sütunlar ve veri türleriyle hizalamak için metin dosyasındaki verileri biçimlendirin. Dış metin dosyalarındaki veri türleri arasındaki hatalı hizalanmış ve veri ambarı tablosu, yük sırasında satırların reddedilmesine neden olur.
- Metin dosyasındaki alanları Sonlandırıcı ile ayırın.  Kaynak verilerinizde bulunmayan bir karakter veya karakter dizisi kullandığınızdan emin olun. [Dış dosya biçimi oluştur](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)ile belirttiğiniz sonlandırıcıyı kullanın.

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. PolyBase kullanarak verileri adanmış SQL havuzu hazırlama tablolarına yükleme

Hazırlama tablosuna veri yüklemek en iyi uygulamadır. Hazırlama tabloları, üretim tablolarının kesintiye uğramadan hataları işleyebilmeniz için izin verir. Hazırlama tablosu, verileri üretim tablolarına eklemeden önce veri dönüştürmeleri için yerleşik olarak dağıtılan sorgu işleme yeteneklerini de kullanmanıza olanak sağlar.

### <a name="options-for-loading-with-polybase"></a>PolyBase ile yükleme seçenekleri

PolyBase ile veri yüklemek için, bu yükleme seçeneklerinden herhangi birini kullanabilirsiniz:

- [T-SQL Ile PolyBase](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) , verileriniz Azure Blob depolama veya Azure Data Lake Store olduğunda iyi bir şekilde çalışabilir. Bu, yükleme işlemi üzerinde en fazla denetim sağlar, ancak dış veri nesneleri tanımlamanızı da gerektirir. Diğer yöntemler, kaynak tablolarını hedef tablolarla eşleştirdiğinizde arka planda bu nesneleri tanımlar.  T-SQL yüklemelerini düzenlemek için Azure Data Factory, SSIS veya Azure işlevlerini kullanabilirsiniz.
- [SSIS Ile PolyBase](/sql/integration-services/load-data-to-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) , kaynak verileriniz SQL Server olduğunda iyi sonuç verir. SSIS, kaynağı hedef tablo eşlemelerine tanımlar ve ayrıca yükü düzenler. SSIS paketleriniz zaten varsa, yeni veri ambarı hedefle çalışacak şekilde paketleri değiştirebilirsiniz.
- [Azure Data Factory (ADF) Ile PolyBase](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) başka bir düzenleme aracıdır.  İşlem hattını tanımlar ve işleri zamanlar.
- [Azure Databricks Ile PolyBase,](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) verileri bir Azure SYNAPSE Analytics tablosundan Databricks veri çerçevesine aktarır ve/veya databricks veri çerçevesindeki verileri PolyBase kullanarak bir Azure SYNAPSE Analytics tablosuna yazar.

### <a name="non-polybase-loading-options"></a>PolyBase yükleme seçenekleri

Verileriniz PolyBase ile uyumlu değilse [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) veya [SqlBulkCopy API 'sini](/dotnet/api/system.data.sqlclient.sqlbulkcopy)kullanabilirsiniz. BCP, Azure Blob depolama alanına geçmeden doğrudan adanmış SQL havuzuna yükler ve yalnızca küçük yüklemeler için tasarlanmıştır. Bu seçeneklerin yük performansı, PolyBase 'den önemli ölçüde daha yavaştır.

## <a name="5-transform-the-data"></a>5. verileri dönüştürme

Veriler hazırlama tablodayken, iş yükünüzün gerektirdiği dönüştürmeleri gerçekleştirin. Sonra verileri bir üretim tablosuna taşıyın.

## <a name="6-insert-the-data-into-production-tables"></a>6. verileri üretim tablolarına ekleyin

INSERT INTO... SELECT ifadesinin verileri hazırlama tablosundan kalıcı tabloya taşınıyor.

Bir ETL işlemi tasarlarken, işlemi küçük bir test örneğinde çalıştırmayı deneyin. Tablodaki 1000 satırı bir dosyaya ayıklamayı deneyin, Azure 'a taşıyın ve sonra hazırlama tablosuna yüklemeyi deneyin.

## <a name="partner-loading-solutions"></a>İş ortağı yükleme çözümleri

İş ortaklarımızın birçoğu çözüm yüklüyor. Daha fazla bilgi edinmek için [çözüm iş ortaklarımızın](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)listesini inceleyin.

## <a name="next-steps"></a>Sonraki adımlar

Yükleme Kılavuzu için bkz. [veri yükleme kılavuzu](data-loading-best-practices.md).