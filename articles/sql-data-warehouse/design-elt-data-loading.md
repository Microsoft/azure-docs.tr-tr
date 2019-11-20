---
title: ETL yerine,, ELT tasarlayın
description: ETL yerine, verileri veya Azure SQL veri ambarını yüklemek için bir ayıklama, yükleme ve dönüştürme (ELT) işlemi tasarlayın.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 11/07/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9220bf0cf94eaae6ddc945e83deac2a6041158d2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748514"
---
# <a name="data-loading-strategies-for-azure-sql-data-warehouse"></a>Azure SQL veri ambarı için veri yükleme stratejileri

Geleneksel SMP veri ambarları, verileri yüklemek için bir ayıklama, dönüştürme ve yükleme (ETL) işlemi kullanır. Azure SQL veri ambarı, bilgi işlem ve depolama kaynaklarının ölçeklenebilirlik ve esnekliğinden yararlanan, yüksek düzeyde paralel işleme (MPP) mimarisidir. Bir ayıklama, yükleme ve dönüştürme (ELT) işleminin kullanılmasıyla, MPP 'den yararlanabilir ve yüklemeden önce verileri dönüştürmek için gereken kaynakları ortadan kaldırabilir. SQL veri ambarı BCP ve SQL BulkCopy API gibi popüler SQL Server seçenekleri de dahil olmak üzere birçok yükleme yöntemini desteklese de, verileri yüklemenin en hızlı ve en ölçeklenebilir yolu PolyBase dış tabloları ve [Copy deyimleridir](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (Önizleme).  PolyBase ve COPY ifadesiyle, Azure Blob depolamada depolanan dış verilere veya T-SQL dili üzerinden Azure Data Lake Store erişebilirsiniz. SQL veri ambarı 'na yükleme yaparken en fazla esneklik için, COPY ifadesinin kullanılması önerilir. 

> [!NOTE]  
> COPY deyimleri Şu anda genel önizlemededir. Geri bildirim sağlamak için şu dağıtım listesine e-posta gönderin: sqldwcopypreview@service.microsoft.com.
>
        
 
> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>ELT nedir?

Ayıklama, yükleme ve dönüştürme (ELT), verilerin bir kaynak sistemden ayıklandığı, bir veri ambarına yüklendiği ve sonra dönüştürülebileceği bir işlemdir. 

SQL veri ambarı için ELT 'ı uygulamaya yönelik temel adımlar şunlardır:

1. Kaynak verileri metin dosyalarına ayıklayın.
2. Verileri Azure Blob depolama alanına veya Azure Data Lake Store ekleyin.
3. Verileri yükleme için hazırlayın.
4. PolyBase veya COPY komutuyla verileri SQL veri ambarı hazırlama tablolarına yükleyin. 
5. Verileri dönüştürün.
6. Verileri üretim tablolarına ekleyin.


PolyBase yükleme öğreticisi için bkz. [Azure Blob depolamadan Azure SQL veri ambarı 'na veri yüklemek Için PolyBase kullanma](load-data-from-azure-blob-storage-using-polybase.md).

Daha fazla bilgi için bkz. [desenleri web günlüğü yükleme](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. kaynak verileri metin dosyalarına ayıklayın

Kaynak sisteminizden veri alma, depolama konumuna bağlıdır.  Amaç, verileri PolyBase 'e ve kopya desteklenen sınırlandırılmış metin veya CSV dosyalarını taşımaktır. 

### <a name="polybase-and-copy-external-file-formats"></a>PolyBase ve dış dosya biçimlerini Kopyala

PolyBase ve COPY ifadesiyle, UTF-8 ve UTF-16 kodlamalı sınırlandırılmış metin veya CSV dosyalarından veri yükleyebilirsiniz. Ayrılmış metin veya CSV dosyalarına ek olarak, ORC ve Parquet gibi Hadoop dosya biçimlerinden yüklenir. PolyBase ve COPY deyimleri, verileri gzip ve Snappy sıkıştırılmış dosyalarından de yükleyebilir. Genişletilmiş ASCII, sabit genişlikli biçim ve WinZip veya XML gibi iç içe biçimler desteklenmez. SQL Server dışarı aktarıyorsanız, verileri sınırlandırılmış metin dosyalarına aktarmak için [bcp komut satırı aracını](/sql/tools/bcp-utility?view=azure-sqldw-latest) kullanabilirsiniz. 

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. verileri Azure Blob depolama alanına veya Azure Data Lake Store ekleyin

Verileri Azure depolama 'ya taşımak için [Azure Blob depolama](../storage/blobs/storage-blobs-introduction.md) alanına veya [Azure Data Lake Store Gen2](../data-lake-store/data-lake-store-overview.md)taşıyabilirsiniz. Her iki konumda da veriler metin dosyalarında depolanmalıdır. PolyBase ve COPY deyimleri her iki konumdan de yüklenebilir.

Verileri Azure depolama 'ya taşımak için kullanabileceğiniz araçlar ve hizmetler:

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) hizmeti ağ aktarım hızını, performansı ve öngörülebilirlik geliştirir. ExpressRoute, verilerinizi Azure 'a adanmış bir özel bağlantıyla yönlendiren bir hizmettir. ExpressRoute bağlantıları, verileri genel İnternet üzerinden yönlendirmez. Bağlantılar, genel İnternet üzerinden tipik bağlantılardan daha fazla güvenilirlik, daha hızlı hız, daha düşük gecikme süreleri ve daha yüksek güvenlik sunar.
- [AZCopy yardımcı programı](../storage/common/storage-moving-data.md) , verileri genel Internet üzerinden Azure depolama 'ya taşıtabilecek. Bu, veri boyutlarınızın 10 TB 'den küçük olması durumunda geçerlidir. AZCopy ile düzenli olarak yükleme gerçekleştirmek için, kabul edilebilir olup olmadığını görmek için ağ hızını test edin. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) , yerel sunucunuza yükleyebileceğiniz bir ağ geçidine sahip olabilir. Daha sonra, yerel sunucunuzdaki verileri Azure depolama 'ya taşımak için bir işlem hattı oluşturabilirsiniz. SQL veri ambarı ile Data Factory kullanmak için bkz. [SQL Data Warehouse 'a veri yükleme](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. verileri yükleme için hazırlama

Depolama hesabınızdaki verileri SQL veri ambarı 'na yüklemeden önce hazırlamanız ve temizlemeniz gerekebilir. Veriler kaynakta olduğunda veri hazırlama gerçekleştirilebilir, verileri metin dosyalarına dışarı aktardığınızda veya veriler Azure Storage 'dayken olur.  Mümkün olduğunca önce verilerle çalışmak en kolay yoldur.  

### <a name="define-external-tables"></a>Dış tabloları tanımlama

PolyBase kullanıyorsanız, yüklemeden önce veri Ambarınızda dış tablolar tanımlamanız gerekir. COPY ifadesinin dış tabloları gerekli değildir. PolyBase, Azure depolama 'daki verileri tanımlamak ve verilere erişmek için dış tabloları kullanır. Dış tablo, veritabanı görünümüne benzer. Dış tablo tablo şemasını içerir ve veri ambarının dışında depolanan verileri gösterir. 

Dış tabloları tanımlama, veri kaynağını, metin dosyalarının biçimini ve tablo tanımlarını belirtmeyi içerir. T-SQL sözdizimi konuları ihtiyacınız olacak:
- [DıŞ VERI KAYNAĞı OLUŞTUR](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest)
- [DıŞ TABLO OLUŞTUR](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest)

Parquet yüklenirken SQL DW ile veri türü eşlemesi:

| **Parquet veri türü** |                      **SQL veri türü**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boole        |                             sürümleri                              |
|        double         |                            float                             |
|         float         |                             gerçek                             |
|        double         |                            etmenize                             |
|        double         |                          küçük para                          |
|        string         |                            nchar                             |
|        string         |                           nvarchar                           |
|        string         |                             Char                             |
|        string         |                           varchar                            |
|        ý         |                            ý                            |
|        ý         |                          ikili                           |
|       timestamp       |                             date                             |
|       timestamp       |                        girişin                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           datetime                           |
|       timestamp       |                             time                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

Dış nesne oluşturma hakkında bir örnek için, yükleme öğreticisindeki [dış tablo oluşturma](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) adımına bakın.

### <a name="format-text-files"></a>Metin dosyalarını biçimlendirme

PolyBase kullanıyorsanız, tanımlanan dış nesnelerin metin dosyalarının satırlarını dış tablo ve dosya biçimi tanımıyla hizalamaları gerekir. Metin dosyasının her satırındaki veriler tablo tanımıyla hizalanmalıdır.
Metin dosyalarını biçimlendirmek için:

- Verileriniz ilişkisel olmayan bir kaynaktan geliyorsa, onu satırlara ve sütunlara dönüştürmeniz gerekir. Verilerin ilişkisel veya ilişkisel olmayan bir kaynaktan olup olmadığı, verileri yüklemeyi planladığınız tablonun sütun tanımlarına göre hizalanacak şekilde dönüştürülmesi gerekir. 
- Metin dosyasındaki verileri, SQL veri ambarı hedef tablosundaki sütunlar ve veri türleriyle hizalamak için biçimlendirin. Dış metin dosyalarındaki veri türleri arasındaki hatalı hizalanmış ve veri ambarı tablosu, yük sırasında satırların reddedilmesine neden olur.
- Metin dosyasındaki alanları Sonlandırıcı ile ayırın.  Kaynak verilerinizde bulunmayan bir karakter veya karakter dizisi kullandığınızdan emin olun. [Dış dosya biçimi oluştur](/sql/t-sql/statements/create-external-file-format-transact-sql)ile belirttiğiniz sonlandırıcıyı kullanın.


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase-or-the-copy-statement"></a>4. PolyBase veya COPY ifadesini kullanarak verileri SQL veri ambarı hazırlama tablolarına yükleyin

Hazırlama tablosuna veri yüklemek en iyi uygulamadır. Hazırlama tabloları, üretim tablolarının kesintiye uğramadan hataları işleyebilmeniz için izin verir. Hazırlama tablosu, verileri üretim tablolarına eklemeden önce veri dönüştürmeleri için SQL veri ambarı MPP kullanma fırsatını de sağlar. KOPYALAMA ile hazırlama tablosuna yükleme sırasında tablonun önceden oluşturulması gerekir.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>PolyBase ve COPY ifadesiyle yükleme seçenekleri

PolyBase ile veri yüklemek için, bu yükleme seçeneklerinden herhangi birini kullanabilirsiniz:

- [T-SQL Ile PolyBase](load-data-from-azure-blob-storage-using-polybase.md) , verileriniz Azure Blob depolama veya Azure Data Lake Store olduğunda iyi bir şekilde çalışabilir. Bu, yükleme işlemi üzerinde en fazla denetim sağlar, ancak dış veri nesneleri tanımlamanızı da gerektirir. Diğer yöntemler, kaynak tablolarını hedef tablolarla eşleştirdiğinizde arka planda bu nesneleri tanımlar.  T-SQL yüklemelerini düzenlemek için Azure Data Factory, SSIS veya Azure işlevlerini kullanabilirsiniz. 
- [SSIS Ile PolyBase](/sql/integration-services/load-data-to-sql-data-warehouse) , kaynak verileriniz SQL Server, şirket içinde veya bulutta SQL Server olduğunda iyi çalışacaktır. SSIS, kaynağı hedef tablo eşlemelerine tanımlar ve ayrıca yükü düzenler. SSIS paketleriniz zaten varsa, yeni veri ambarı hedefle çalışacak şekilde paketleri değiştirebilirsiniz. 
- [Azure Data Factory (ADF) Ile PolyBase ve Copy deyimleri](sql-data-warehouse-load-with-data-factory.md) başka bir düzenleme aracıdır.  İşlem hattını tanımlar ve işleri zamanlar. 
- [Azure Databricks Ile PolyBase,](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) bir SQL veri ambarı tablosundan Databricks veri çerçevesine veri aktarır ve/veya databricks veri çerçevesindeki verileri PolyBase kullanarak bir SQL veri ambarı tablosuna yazar.

### <a name="other-loading-options"></a>Diğer yükleme seçenekleri

PolyBase ve COPY deyimlerinin yanı sıra [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest) veya [SqlBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx)'sini de kullanabilirsiniz. BCP, Azure Blob depolama alanına geçmeden doğrudan SQL veri ambarı 'na yüklenir ve yalnızca küçük yüklemeler için tasarlanmıştır. Bu seçeneklerin yük performansı, PolyBase ve COPY deyiminden daha yavaştır. 


## <a name="5-transform-the-data"></a>5. verileri dönüştürme

Veriler hazırlama tablodayken, iş yükünüzün gerektirdiği dönüştürmeleri gerçekleştirin. Sonra verileri bir üretim tablosuna taşıyın.


## <a name="6-insert-the-data-into-production-tables"></a>6. verileri üretim tablolarına ekleyin

INSERT INTO... SELECT ifadesinin verileri hazırlama tablosundan kalıcı tabloya taşınıyor. 

Bir ETL işlemi tasarlarken, işlemi küçük bir test örneğinde çalıştırmayı deneyin. Tablodaki 1000 satırı bir dosyaya ayıklamayı deneyin, Azure 'a taşıyın ve sonra hazırlama tablosuna yüklemeyi deneyin. 


## <a name="partner-loading-solutions"></a>İş ortağı yükleme çözümleri

İş ortaklarımızın birçoğu çözüm yüklüyor. Daha fazla bilgi edinmek için [çözüm iş ortaklarımızın](sql-data-warehouse-partner-business-intelligence.md)listesini inceleyin. 


## <a name="next-steps"></a>Sonraki adımlar

Yükleme Kılavuzu için bkz. [veri yükleme kılavuzu](guidance-for-loading-data.md).


