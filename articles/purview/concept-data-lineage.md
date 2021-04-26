---
title: Azure purview 'da Data kökenini (Önizleme)
description: Data kökenini kavramlarını açıklar.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 476355f41de5e0e6aaffdedea8947cab5221767a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200741"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Azure purview Veri Kataloğu istemcisinde veri kökenini

Bu makalede, Azure purview Veri Kataloğu 'nda Data kökenini 'a genel bakış sunulmaktadır. Ayrıca, veri sistemlerinin kökenini verileri yakalamak için katalog ile nasıl tümleştirebileceğinizi ayrıntılarıyla açıklamaktadır. Takip, kuruluşunuzun veri kuruluşlarının farklı bölümlerinde bulunan veriler için kökenini ve aşağıdakiler dahil farklı hazırlık düzeylerinde bulunabilir:

- Çeşitli platformlardan hazırlanan tamamen ham veri
- Dönüştürülen ve hazırlanan veriler
- Görselleştirme platformları tarafından kullanılan veriler.

## <a name="use-cases"></a>Kullanım Örnekleri

Veri kökenini, verilerin kaynağını kapsayan yaşam döngüsü ve verilerin Emlak sırasında zaman içinde taşındığı durumlar olarak büyük ölçüde anlaşılmıştır. Sorun giderme, temel neden veri işlem hatları ve hata ayıklama gibi farklı türlerde çapraz arama senaryolarında kullanılır. Kökenini Ayrıca, veri kalitesi analizi, uyumluluk ve "ne tür" senaryolar için genellikle etki çözümlemesi olarak bahsedildiğinde kullanılır. Kökenini, verilerin nasıl dönüştürüldüğünü de dahil olmak üzere kaynaktan hedefe taşınan verileri göstermek için görsel olarak gösterilir. Çoğu kurumsal veri ortamının karmaşıklığı söz konusu olduğunda, bu görünümler, çevre veri noktalarının bir birleştirme veya maskelemeden anlaşılması zor olabilir.

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Azure purview Veri Kataloğu 'nda kökenini deneyimi

Takip Veri Kataloğu, kökenini bilgilerini ayıklamak için diğer veri işleme, depolama ve analiz sistemleriyle bağlantı sağlar. Bilgiler, katalogda genel, senaryoya özel bir kökenini deneyimini temsil edecek şekilde birleştirilir.

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="blob mağazasından Power BI panoya kopyalanmış olan verileri gösteren bitiş son kökenini":::

Verileriniz, veri ayıklama, dönüşüm (ETL/ELT sistemleri), analiz ve Görselleştirme sistemleri yapan sistemleri içerebilir. Sistemlerin her biri, sistem sınırları içindeki verilerin durumunu ve kalitesini açıklayan zengin statik ve işletimsel meta verileri yakalar. Bir veri kataloğunda kökenini hedefi, her bir veri sisteminden mümkün olan en düşük grede hareket, dönüşüm ve işletimsel meta verileri ayıklamaktır.

Aşağıdaki örnek, veri kataloğu 'nun kökenini için her bir sistem için bağlanacağı birden fazla sistem arasında taşınan verilerin tipik bir kullanım durumdur.

- Data Factory, şirket içi/ham bölgeden verileri buluttaki bir giriş bölgesine kopyalar. 
- SYNAPSE gibi veri işleme sistemleri, Databricks verileri, not defterlerini kullanarak, giriş bölgesinden verileri işlem ve Not defterleri aracılığıyla seçkin bölgeye dönüştürür.
- En iyi sorgu performansı ve toplama için verileri analitik modellere daha fazla işleme. 
- Veri Görselleştirme sistemleri, bir bı panosu, ML denemeleri vb. oluşturmak için meta veri modelleri aracılığıyla veri kümelerini ve işlemleri kullanacaktır.

## <a name="lineage-granularity"></a>Kökenini ayrıntı düzeyi

Bu bölümde, kökenini bilgilerinin bir veri kataloğu tarafından toplanarak ayrıntı düzeyi hakkındaki ayrıntılar ele alınmaktadır. Bu ayrıntı düzeyi, yapılmakta olan veri sistemlerine göre farklılık gösterebilir.

### <a name="entity-level-lineage-sources--process--targets"></a>Varlık düzeyi kökenini: kaynak > Işlem > hedef 

- Kökenini bir grafik olarak temsil edilir, genellikle bir işlem sistemi tarafından çağrılan bir işlemle bağlantılı veri depolama sistemlerindeki kaynak ve hedef varlıkları içerir. 
- Veri sistemleri, temel alınan veri sisteminin fiziksel nesnesine başvuran benzersiz bir nesne oluşturmak ve raporlamak için veri kataloğuna bağlanır, örneğin: SQL saklı yordamı, Not defterleri, vb.
- Sahiplik gibi ek meta veriler içeren yüksek uygunluğa sahip kökenini, kaynak & hedef varlıkları için okunabilir bir biçimde kökenini göstermek üzere yakalanır. Örneğin: kökenini bölümler veya dosya düzeyi yerine Hive tablo düzeyinde.

### <a name="column-or-attribute-level-lineage"></a>Sütun veya öznitelik düzeyi kökenini

Hedef varlıkta öznitelik (ler) oluşturmak veya türetmek için kullanılan bir kaynak varlığın özniteliklerini tanımla. Kaynak özniteliğin adı bir hedefte korunabilir veya yeniden adlandırılabilir. ADF gibi sistemler, şirket içi ortamdan buluta tek bir kopya gerçekleştirebilir. Örneğin: `Table1/ColumnA -> Table2/ColumnA`.

### <a name="process-execution-status"></a>İşlem yürütme durumu

Kök neden analizi ve veri kalitesi senaryolarını desteklemek için, veri işleme sistemlerindeki işlerin yürütme durumunu yakalarız. Bu gereksinimin, diğer veri işleme sistemlerinin izleme yeteneklerini değiştirme konusunda hiçbir şey yoktur, ancak bu, hedef bunları değiştirmez. 

## <a name="summary"></a>Özet

Kökenini, kalite, güven ve denetim senaryolarını desteklemek üzere purview veri kataloğunun kritik bir özelliğidir. Veri Kataloğu 'nun amacı, ortamınızdaki tüm veri sistemlerinin doğal olarak bağlanıp kökenini raporlabileceği sağlam bir çerçeve inşa olmaktır. Meta veriler kullanılabilir duruma getirildikten sonra Veri Kataloğu veri sistemleri tarafından sağlanan meta verileri bir araya getirerek veri yönetimi kullanım örneklerine güç sağlar.

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: Azure portal bir Azure purview hesabı oluşturun](create-catalog-portal.md)
* [Hızlı başlangıç: Azure PowerShell/Azure CLı kullanarak Azure purview hesabı oluşturma](create-catalog-powershell.md)
* [Hızlı başlangıç: purview Studio 'Yu kullanma](use-purview-studio.md)
