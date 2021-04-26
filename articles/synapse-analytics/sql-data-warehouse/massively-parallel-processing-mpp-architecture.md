---
title: Adanmış SQL Havuzu (eski adıyla SQL DW) mimarisi
description: Azure SYNAPSE Analytics 'te adanmış SQL havuzunun (eski adıyla SQL DW), yüksek performans ve ölçeklenebilirlik elde etmek için Azure depolama ile dağıtılmış sorgu işleme özelliklerini nasıl birleştirdiğini öğrenin.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 0e87451531750e502f67dc30e6fbd26c8c944d22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678602"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-architecture-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL Havuzu (eski adıyla SQL DW) mimarisi

Azure Synapse Analytics, kurumsal veri ambarı özellikleriyle Büyük Veri analizini bir araya getiren bir analiz hizmetidir. Bu, verileri koşullarınızda sorgulama özgürlüğü sunar.

> [!NOTE]
>[Azure SYNAPSE Analytics belgelerini](../overview-what-is.md)inceleyin.
>


> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-architecture-components"></a>SYNAPSE SQL mimarisi bileşenleri

[ADANMıŞ SQL Havuzu (eski ADıYLA SQL DW)](sql-data-warehouse-overview-what-is.md) , verilerin birden çok düğüm arasında işlem işlemesini dağıtmak için bir genişleme mimarisinden yararlanır. Ölçek birimi, [veri ambarı birimi](what-is-a-data-warehouse-unit-dwu-cdwu.md)olarak bilinen bir işlem gücü soyutlamasıdır. İşlem depolama alanından ayrıdır ve bu işlem, sisteminizi sisteminizdeki verilerden bağımsız olarak ölçeklendirmenizi sağlar.

![Adanmış SQL Havuzu (eski adıyla SQL DW) mimarisi](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Adanmış SQL Havuzu (eski adıyla SQL DW), düğüm tabanlı bir mimari kullanır. Uygulamalar bir denetim düğümüne bağlanır ve T-SQL komutları verebilir. Denetim düğümü, paralel işleme için sorguları en iyi duruma getirirken dağıtılmış sorgu altyapısını barındırır ve sonra işlerini paralel olarak yapmak üzere işlem düğümlerine geçirir.

İşlem düğümleri tüm kullanıcı verilerini Azure Depolama’da depolar ve paralel sorgular çalıştırır. Veri Taşıma Hizmeti (DMS), sorguları paralel olarak çalıştırmak ve doğru sonuçlar döndürmek için verileri düğümler arasında taşıyan, sistem düzeyindeki bir dahili hizmettir.

Ayrılmış depolama ve işlem ile ayrılmış bir SQL Havuzu (eski adıyla SQL DW) kullanılırken şunları yapabilirsiniz:

- Depolama gereksinimlerinizin ne olursa olsun bağımsız işlem gücü.
- İşlem gücünü, ayrılmış bir SQL havuzunda (eski adıyla SQL DW), verileri taşımadan büyütün veya küçültün.
- Verileri olduğu gibi bırakıp işlem kapasitesini duraklatır, böylece yalnızca depolama için ödeme yaparsınız.
- Çalışma saatleri içinde işlem kapasitesini sürdürme.

### <a name="azure-storage"></a>Azure Depolama

Adanmış SQL havuzu SQL (eski adıyla SQL DW), kullanıcı verilerinizi güvende tutmak için Azure depolama özelliğinden yararlanır.  Verileriniz Azure depolama tarafından depolandığından ve yönetildiğinden, depolama tüketiminize yönelik ayrı bir ücret alınır. Veriler, sistem performansını iyileştirmek için **dağıtımlarla** birleştirilir. Tabloyu tanımlarken verileri dağıtmak için hangi parçalama deseninin kullanılacağını seçebilirsiniz. Bu parçalı desenler desteklenir:

- Karma
- Hepsini Bir Kez Deneme
- Çoğaltma

### <a name="control-node"></a>Denetim düğümü

Denetim düğümü mimarinin beynidir. Tüm uygulamalarla ve bağlantılarla etkileşim kuran ön uçtur. Dağıtılmış sorgu altyapısı, Paralel sorguları iyileştirmek ve koordine etmek için denetim düğümünde çalışır. Bir T-SQL sorgusu gönderdiğinizde, denetim düğümü onu paralel olarak her dağıtıma karşı çalışan sorgulara dönüştürür.

### <a name="compute-nodes"></a>İşlem düğümleri

İşlem düğümleri, hesaplama gücü sağlar. Dağıtım, işlenmek üzere Işlem düğümlerine eşlenir. Daha fazla işlem kaynağı için ödeme yaparken, dağıtımlar kullanılabilir Işlem düğümlerine yeniden eşlenir. İşlem düğümlerinin sayısı 1 ile 60 arasında değişir ve SYNAPSE SQL hizmet düzeyiyle belirlenir.

Her Işlem düğümünün sistem görünümlerinde görünür bir düğüm KIMLIĞI vardır. Adları sys.pdw_nodes ile başlayan sistem görünümlerindeki node_id sütununa bakarak Işlem düğümü KIMLIĞINI görebilirsiniz. Bu sistem görünümlerinin listesi için bkz. [SYNAPSE SQL System views](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="data-movement-service"></a>Veri Taşıma Hizmeti

Veri taşıma hizmeti (DMS), Işlem düğümleri arasında veri hareketini koordine eden veri aktarımı teknolojisidir. Bazı sorgular paralel sorguların doğru sonuçlar döndürmesini sağlamak için veri hareketi gerektirir. Veri taşıma gerektiğinde, DMS doğru verinin doğru konuma gelmesini sağlar.

## <a name="distributions"></a>Dağıtımları

Dağıtım, dağıtılmış veriler üzerinde çalışan paralel sorgular için temel depolama ve işleme birimidir. SYNAPSE SQL bir sorgu çalıştırdığında, iş paralel olarak çalışan 60 daha küçük sorguya bölünür.

60 küçük bir sorgu, veri dağıtımlarından birinde çalışır. Her Işlem düğümü bir veya daha fazla 60 dağıtımlarını yönetir. En fazla işlem kaynağına sahip adanmış bir SQL havuzunun (eski adıyla SQL DW) Işlem düğümü başına bir dağıtımı vardır. En düşük işlem kaynaklarıyla ayrılmış bir SQL Havuzu (eski adıyla SQL DW), tek bir işlem düğümündeki tüm dağıtımlarla birlikte bulunur.  

## <a name="hash-distributed-tables"></a>Karma dağıtılmış tablolar

Karma dağıtılmış bir tablo, büyük tablolarsa birleştirmeler ve toplamalar için en yüksek sorgu performansını sunabilir.

Verileri karma olarak dağıtılmış bir tabloya bölmek için, her bir satırı bir dağıtıma göre kesin şekilde atamak üzere bir karma işlev kullanılır. Tablo tanımında sütunlardan biri dağıtım sütunu olarak atanır. Karma işlevi, her satırı bir dağıtıma atamak için dağıtım sütunundaki değerleri kullanır.

Aşağıdaki diyagramda, tam (dağıtılmamış bir tablonun) karma olarak dağıtılan tablo olarak nasıl depolandığı gösterilmektedir.

![Dağıtılmış tablo](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Dağıtılmış tablo")  

- Her satır bir dağıtıma aittir.  
- Belirleyici bir karma algoritması her satırı bir dağıtıma atar.  
- Dağıtım başına tablo satırı sayısı, farklı tablo boyutları tarafından gösterilen şekilde değişir.

Bir dağıtım sütununun seçimi için, ayrımlık, veri eğriltme ve sistemde çalışan sorgu türleri gibi performans konuları vardır.

## <a name="round-robin-distributed-tables"></a>Hepsini bir kez deneme dağıtılmış tabloları

Hepsini bir kez deneme tablosu, yüklemeler için hazırlama tablosu olarak kullanıldığında hızlı performans oluşturmak ve sağlamak için en basit tablodur.

Hepsini bir kez deneme dağıtılmış tablosu, verileri tabloda eşit olarak dağıtır ancak başka bir iyileştirme yapmaz. Önce bir dağıtım rastgele seçilir ve sonra, satır arabellekleri dağıtıma sırayla atanır. Verileri hepsini bir kez deneme tablosuna yüklemek hızlı bir işlemdir ancak sorgu performansı genellikle karma dağıtılmış tablolar ile daha iyi olabilir. Hepsini bir kez deneme tablolarında birleşimler, ek zaman alan reshuffling verileri gerektirir.

## <a name="replicated-tables"></a>Çoğaltılmış Tablolar

Çoğaltılmış bir tablo, küçük tablolar için en hızlı sorgu performansını sağlar.

Çoğaltılan bir tablo, her işlem düğümündeki tablonun tam bir kopyasını önbelleğe alır. Sonuç olarak, bir tablo çoğaltıldığında bir birleştirme veya toplama öncesinde işlem düğümleri arasında verileri aktarma gereksinimi ortadan kalkar. Çoğaltılmış tablolar, küçük tablolar ile en iyi şekilde kullanılır. Ek depolama alanı gereklidir ve büyük tabloları pratik hale getirmek için veri yazarken tahakkuk eden ek yük vardır.  

Aşağıdaki diyagramda, her işlem düğümündeki ilk dağıtım üzerinde önbelleğe alınmış bir çoğaltılan tablo gösterilmektedir.  

![Çoğaltılmış tablo](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Çoğaltılmış tablo")

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure SYNAPSE hakkında bir bit öğrenmiş olduğunuza göre, [Özel BIR SQL havuzunun (eski ADıYLA SQL DW)](create-data-warehouse-portal.md) nasıl hızlı bir şekilde oluşturulduğunu ve [örnek verileri yüklemeyi](./load-data-from-azure-blob-storage-using-copy.md)öğrenin. Azure’da yeniyseniz yeni terimlerle karşılaşabileceğinizi için [Azure sözlüğünü](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) yararlı bulabilirsiniz. Ya da diğer Azure SYNAPSE kaynaklarından bazılarına bakın.  

- [Başarı hikayeleri](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Bloglar](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Özellik istekleri](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Videolar](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Destek bileti oluşturun](sql-data-warehouse-get-started-create-support-ticket.md)
- [Soru sayfası Microsoft Q&](/answers/topics/azure-synapse-analytics.html)
- [Stack Overflow Forumu](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)