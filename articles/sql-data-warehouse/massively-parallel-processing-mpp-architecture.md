---
title: Azure SYNAPSE Analytics (eski adıyla SQL DW) mimarisi
description: Azure SYNAPSE Analytics 'in (eski adıyla SQL DW), yüksek performans ve ölçeklenebilirlik elde etmek için Azure depolama ile çok büyük bir paralel işleme (MPP) birleştirdiğini öğrenin.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: ea9629c63fcab97ba8ba83cd88592c37ae41818a
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646397"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure SYNAPSE Analytics (eski adıyla SQL DW) mimarisi 

Azure SYNAPSE, kurumsal veri depolama ve büyük veri analizlerini birlikte getiren sınırsız bir analiz hizmetidir. Bu sayede, daha az sunucu veya sağlanan kaynakları kullanarak koşullarınızda verileri, uygun ölçekte sorgulama özgürlüğü sunar. Azure SYNAPSE, bu iki çalışma LDS 'yi, anında ve makine öğrenimi ihtiyaçları için veri alma, hazırlama, yönetme ve sunma konularında birleştirilmiş bir deneyimle birlikte sunar.

 Azure SYNAPSE dört bileşene sahiptir:
- SQL Analytics: tam T-SQL tabanlı analiz 
    - SQL Havuzu (sağlanan DWU başına ödeme) – genel olarak kullanılabilir
    - İstek üzerine SQL (işlenen TB başına ödeme) – (Önizleme)
- Spark: derin tümleşik Apache Spark (Önizleme) 
- Veri tümleştirme: karma veri tümleştirmesi (Önizleme)
- Studio: birleştirilmiş kullanıcı deneyimi.  (Önizleme)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="sql-analytics-mpp-architecture-components"></a>SQL Analytics MPP mimari bileşenleri

[SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) , verilerin birden çok düğüm arasında işlem işlemesini dağıtmak için bir genişleme mimarisinden yararlanır. Ölçek birimi, [veri ambarı birimi](what-is-a-data-warehouse-unit-dwu-cdwu.md)olarak bilinen bir işlem gücü soyutlamasıdır. İşlem, sisteminizdeki verilerden bağımsız olarak işlem ölçeğini ölçeklendirmenizi sağlayan depolamadan ayrıdır.

![SQL Analytics mimarisi](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Analytics, düğüm tabanlı bir mimari kullanır. Uygulamalar, T-SQL komutlarını, SQL Analytics için tek giriş noktası olan bir denetim düğümüne bağlanır ve bu komutlara verebilir. Denetim düğümü, paralel işleme için sorguları en iyi duruma getirir ve sonra işlerini paralel olarak yapmak üzere işlem düğümlerine geçirir. 

Işlem düğümleri tüm Kullanıcı verilerini Azure Storage 'da depolar ve Paralel sorguları çalıştırır. Veri taşıma hizmeti (DMS), sorguları paralel olarak çalıştırmak ve doğru sonuçları döndürmek için gereken verileri düğümler arasında taşınan sistem düzeyinde bir iç hizmettir. 

Ayrılmış depolama ve işlem ile SQL Analytics 'in kullanımı şunları yapabilir:

* Depolama gereksinimlerinizin ne olursa olsun bağımsız işlem gücü.
* Veri taşımadan bir SQL Havuzu (veri ambarı) içinde işlem gücünü büyütün veya küçültün.
* İşlem kapasitesini, verileri bozulmadan bırakarak yalnızca depolama için ödeme yaparsınız.
* Çalışma saatleri içinde işlem kapasitesini sürdürür.

### <a name="azure-storage"></a>Azure depolama alanı

SQL Analytics, kullanıcı verilerinizi güvende tutmak için Azure Storage 'ı kullanır.  Verileriniz Azure depolama tarafından depolandığından ve yönetildiğinden, depolama tüketiminize yönelik ayrı bir ücret alınır. Verilerin kendisi, sistemin performansını iyileştirmek için **dağıtımlarla** birleştirilir. Tabloyu tanımlarken verileri dağıtmak için kullanılacak parçalama düzeninin arasından seçim yapabilirsiniz. Bu parçalı desenler desteklenir:

* Karma
* Hepsini Bir Kez Deneme
* Çoğaltma

### <a name="control-node"></a>Denetim düğümü

Denetim düğümü, mimarinin beyinidir. Tüm uygulamalarla ve bağlantılarla etkileşim kuran ön uçtur. MPP altyapısı, Paralel sorguları iyileştirmek ve koordine etmek için denetim düğümünde çalışır. SQL Analytics 'e bir T-SQL sorgusu gönderdiğinizde, denetim düğümü onu paralel olarak her dağıtıma karşı çalışan sorgulara dönüştürür.

### <a name="compute-nodes"></a>İşlem düğümleri

Işlem düğümleri hesaplama gücü sağlar. Dağıtım, işlenmek üzere Işlem düğümlerine eşlenir. Daha fazla işlem kaynağı için ödeme yaparken, SQL Analytics dağıtımları kullanılabilir Işlem düğümlerine yeniden eşler. İşlem düğümlerinin sayısı 1 ile 60 arasında değişir ve SQL Analytics hizmet düzeyi tarafından belirlenir.

Her Işlem düğümünün sistem görünümlerinde görünür bir düğüm KIMLIĞI vardır. Adları sys. PDW _nodes ile başlayan sistem görünümlerindeki node_id sütununa bakarak Işlem düğümü KIMLIĞINI görebilirsiniz. Bu sistem görünümlerinin listesi için bkz. [MPP sistem görünümleri](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Veri taşıma hizmeti
Veri taşıma hizmeti (DMS), Işlem düğümleri arasında veri hareketini koordine eden veri aktarımı teknolojisidir. Bazı sorgular paralel sorguların doğru sonuçlar döndürmesini sağlamak için veri hareketi gerektirir. Veri taşıma gerektiğinde, DMS doğru verinin doğru konuma gelmesini sağlar. 

## <a name="distributions"></a>Dağıtımları

Dağıtım, dağıtılmış veriler üzerinde çalışan paralel sorgular için temel depolama ve işleme birimidir. SQL Analytics bir sorgu çalıştırdığında, iş paralel olarak çalışan 60 daha küçük sorguya bölünür. 

60 küçük bir sorgu, veri dağıtımlarından birinde çalışır. Her Işlem düğümü bir veya daha fazla 60 dağıtımlarını yönetir. En fazla işlem kaynağına sahip bir SQL havuzunda Işlem düğümü başına bir dağıtım vardır. En düşük işlem kaynaklarına sahip bir SQL havuzunda tek bir işlem düğümündeki tüm dağıtımlar vardır.  

## <a name="hash-distributed-tables"></a>Karma Dağıtılmış tablolar
Karma Dağıtılmış bir tablo, büyük tablolardaki birleşimler ve toplamalar için en yüksek sorgu performansını sunabilir. 

Verileri karma olarak dağıtılan bir tabloya bölmek için SQL Analytics, her bir satırı bir dağıtıma göre belirleyici olarak atamak için bir karma işlevi kullanır. Tablo tanımında, sütunlardan biri dağıtım sütunu olarak atanır. Karma işlevi, her bir satırı bir dağıtıma atamak için dağıtım sütunundaki değerleri kullanır.

Aşağıdaki diyagramda, tam (dağıtılmamış bir tablonun) karma olarak dağıtılan tablo olarak nasıl depolandığı gösterilmektedir. 

![Dağıtılmış tablo](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Dağıtılmış tablo")  

* Her satır bir dağıtıma aittir.  
* Belirleyici bir karma algoritması her satırı bir dağıtıma atar.  
* Dağıtım başına tablo satırı sayısı, farklı tablo boyutları tarafından gösterilen şekilde değişir.

Bir dağıtım sütununun seçimi için, ayrımlık, veri eğriltme ve sistemde çalışan sorgu türleri gibi performans konuları vardır.

## <a name="round-robin-distributed-tables"></a>Hepsini bir kez deneme dağıtılmış tablolar
Hepsini bir kez deneme tablosu, yüklemeler için hazırlama tablosu olarak kullanıldığında hızlı performans oluşturmak ve sağlamak için en basit tablodur.

Hepsini bir kez deneme dağıtılmış tablo, verileri tabloya eşit olarak dağıtır, ancak daha iyi bir iyileştirme yoktur. Önce bir dağıtım rastgele seçilir ve sonra, satır arabellekleri dağıtıma sırayla atanır. Verileri hepsini bir hepsini bir kez deneme tablosuna yüklemek hızlı bir şekilde yapılır, ancak sorgu performansı genellikle karma dağıtılmış tablolarla daha iyi olabilir. Hepsini bir kez deneme tablolarında birleşimler reshuffling veri gerektirir ve bu da ek süre sürer.


## <a name="replicated-tables"></a>Çoğaltılan tablolar
Çoğaltılan bir tablo, küçük tablolar için en hızlı sorgu performansını sağlar.

Çoğaltılan bir tablo, her işlem düğümündeki tablonun tam bir kopyasını önbelleğe alır. Sonuç olarak, bir tablonun çoğaltılması, bir JOIN veya toplanmadan önce işlem düğümleri arasında veri aktarımı gereksinimini ortadan kaldırır. Çoğaltılan tablolar, küçük tablolarla en iyi şekilde kullanılır. Ek depolama alanı gereklidir ve büyük tabloları pratik hale getirmek için veri yazarken tahakkuk eden ek yük vardır.  

Aşağıdaki diyagramda, her işlem düğümündeki ilk dağıtım üzerinde önbelleğe alınan bir çoğaltılan tablo gösterilmektedir.  

![Çoğaltılan tablo](media/sql-data-warehouse-distributed-data/replicated-table.png "Çoğaltılan tablo") 

## <a name="next-steps"></a>Sonraki adımlar
Azure SYNAPSE hakkında biraz bilgi sahibi olduğunuza göre hızlıca [BIR SQL havuzu oluşturma][create a SQL pool] ve [örnek verileri yükleme][load sample data]hakkında bilgi edinin. Azure’da yeniyseniz yeni terimlerle karşılaşabileceğinizi için [Azure sözlüğünü][Azure glossary] yararlı bulabilirsiniz. Ya da diğer Azure SYNAPSE kaynaklarından bazılarına bakın.  

* [Müşteri başarı hikayeleri]
* [Bloglar]
* [Özellik istekleri]
* [Videolar]
* [Müşteri Danışma Ekibi blogları]
* [Destek bileti oluşturma]
* [MSDN forumu]
* [Stack Overflow forumu]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Destek bileti oluşturma]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL pool]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Müşteri başarı hikayeleri]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Bloglar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Müşteri Danışma Ekibi blogları]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Özellik istekleri]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN forumu]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow forumu]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videolar]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
