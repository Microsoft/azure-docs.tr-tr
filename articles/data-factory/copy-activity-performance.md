---
title: Azure Data Factory 'de etkinlik performansı ve ölçeklenebilirlik kılavuzunu kopyala
description: Kopyalama etkinliğini kullandığınızda Azure Data Factory veri hareketinin performansını etkileyen anahtar faktörleri hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 701eaad8d36b352e946ae8d74204876b41ecb53d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678275"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Kopyalama etkinliği performans ve ölçeklenebilirlik Kılavuzu
> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Azure Data Factory sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-copy-activity-performance.md)
> * [Geçerli sürüm](copy-activity-performance.md)

Data Lake veya kurumsal veri ambarından (EDW) Azure 'a büyük ölçekli veri geçişi gerçekleştirmek isteyip istemediğiniz ya da büyük veri analizi için farklı kaynaklardaki verileri Azure 'a dönüştürmek istiyorsanız, en iyi performansı elde etmek ve sorun.  Azure Data Factory, verileri ölçeklendirerek yüksek performanslı ve ölçeklenebilir veri alma işlem hatları oluşturmaya yönelik veri mühendislerine harika bir uyum sağlayan performans, esnek ve ekonomik bir mekanizma sağlar.

Bu makaleyi okuduktan sonra aşağıdaki soruları cevaplayabilirsiniz:

- Veri taşıma ve veri alma senaryolarında ADF kopyalama etkinliğini kullanarak ne düzeyde performans ve ölçeklenebilirlik elde edebilirim?

- ADF kopyalama etkinliğinin performansını ayarlamak için hangi adımları gerçekleştirmeniz gerekir?
- Tek bir kopyalama etkinliğinin çalışması için performansı iyileştirmek üzere ADF performans iyileştirmesi her ne kadar kullanabilir?
- Kopyalama performansını en iyi duruma getirirken ADF dışında hangi diğer faktörler göz önüne

> [!NOTE]
> Kopyalama etkinliğini genel olarak bilmiyorsanız, bu makaleyi kullanmadan önce [kopyalama etkinliğine genel bakış](copy-activity-overview.md) bölümüne bakın.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>ADF kullanarak performans ve ölçeklenebilirlik ulaşılabilir kopyalama

ADF, farklı düzeylerde paralellik sağlayan sunucusuz bir mimari sunar ve bu sayede, ortamınız için veri taşıma aktarım hızını en üst düzeye çıkarmak amacıyla, geliştiricilerin ağ bant genişliğinizi ve depolama ıOPS ve bant genişliğini tamamen kullanmak üzere işlem hatları oluşturmalarına olanak tanır.  Bu, elde ettiğiniz aktarım hızı, kaynak veri deposu, hedef veri deposu ve kaynak ile hedef arasındaki ağ bant genişliği tarafından sunulan minimum aktarım hızını ölçerek tahmin edilebilecek bir anlamına gelir.  Aşağıdaki tabloda, ortamınız için veri boyutuna ve bant genişliği sınırına göre kopyalama süresi hesaplanır. 

| Veri boyutu/ <br/> Bant genişliği | 50 Mbps    | 100 Mb/sn  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 dk    | 1,4 dk   | 0,3 dk   | 0,1 dk  | 0,03 dk | 0,01 dk | 0,0 dk   |
| **10 GB**                   | 27,3 dk   | 13,7 dk  | 2,7 dk   | 1,3 dk  | 0,3 dk  | 0,1 dk  | 0,03 dk  |
| **100 GB**                  | 4,6 saat    | 2,3 saat   | 0,5 saat   | 0,2 saat  | 0,05 Saat | 0,02 Saat | 0,0 saat   |
| **1 TB**                    | 46,6 saat   | 23,3 saat  | 4,7 saat   | 2,3 saat  | 0,5 saat  | 0,2 saat  | 0,05 Saat  |
| **10 TB**                   | 19,4 gün  | 9,7 gün  | 1,9 gün  | 0,9 gün | 0,2 gün | 0,1 gün | 0,02 gün |
| **100 TB**                  | 194,2 gün | 97,1 gün | 19,4 gün | 9,7 gün | 1,9 gün | 1 gün   | 0,2 gün  |
| **1 PB**                    | 64,7 Mo    | 32,4 Mo   | 6,5 Mo    | 3,2 Mo   | 0,6 Mo   | 0,3 Mo   | 0,06 Mo   |
| **10 PB**                   | 647,3 Mo   | 323,6 Mo  | 64,7 Mo   | 31,6 Mo  | 6,5 Mo   | 3,2 Mo   | 0,6 Mo    |

ADF kopyası farklı düzeylerde ölçeklenebilir:

![ADF kopyalama nasıl ölçeklendirilir](media/copy-activity-performance/adf-copy-scalability.png)

- ADF denetim akışı, örneğin [her döngü için](control-flow-for-each-activity.md)kullanarak birden çok kopyalama etkinliğini paralel olarak başlatabilir.
- Tek bir kopyalama etkinliği ölçeklenebilir işlem kaynaklarından yararlanabilir: Azure Integration Runtime kullanırken, her bir kopyalama etkinliği için sunucusuz bir şekilde [en fazla 256 DIUs](#data-integration-units) belirtebilirsiniz; Şirket içinde barındırılan Integration Runtime kullanılırken, makineyi el ile ölçeklendirebilir veya birden fazla makineye ([4 düğüme kadar](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) ölçeklendirebilirsiniz ve tek bir kopyalama etkinliği dosya kümesini tüm düğümlerde bölümleyebilir.
- Tek bir kopyalama etkinliği, [paralel olarak](#parallel-copy)birden çok iş parçacığı kullanarak veri deposundan okur ve yazar.

## <a name="performance-tuning-steps"></a>Performans ayarlama adımları

Kopyalama etkinliğiyle Azure Data Factory hizmetinizin performansını ayarlamak için bu adımları uygulayın.

1. **Bir test veri kümesi seçin ve bir taban çizgisi oluşturun.** Geliştirme aşamasında, bir temsili veri örneğine göre kopyalama etkinliğini kullanarak işlem hattınızı test edin. Seçtiğiniz veri kümesi, normal veri desenlerinizi (klasör yapısı, dosya deseni, veri şeması, vb.) temsil etmelidir ve kopyalama performansını değerlendirmek için yeterince büyük olur, örneğin kopyalama etkinliğinin tamamlanabilmesi için 10 dakika veya daha fazla süre sürer. [Kopyalama etkinliği izlemeyi](copy-activity-overview.md#monitoring)izleyen yürütme ayrıntılarını ve performans özelliklerini toplayın.

2. **Tek bir kopyalama etkinliğinin performansını en üst düzeye çıkarma**:

   İle başlamak için, ilk olarak tek bir kopyalama etkinliği kullanarak performansı en üst düzeye çıkarmanızı öneririz.

   **Kopyalama etkinliği bir Azure Integration Runtime üzerinde yürütülürse:**

   [Veri tümleştirme birimleri (DIU)](#data-integration-units) ve [paralel kopya](#parallel-copy) ayarları için varsayılan değerlerle başlayın.  Bir performans testi çalıştırması gerçekleştirin ve ayrıca elde edilen performans ve paralel kopyalar için kullanılan gerçek değerleri göz önünde bulunun.  Kullanılan çalıştırma sonuçları ve performans ayarlarının nasıl toplanacağı hakkında [Etkinlik izlemeyi kopyalama](copy-activity-overview.md#monitoring) konusuna bakın.

   Artık, her zaman DIU ayarı için değeri katlayarak ek performans testi çalıştırmaları gerçekleştirin.  Alternatif olarak, varsayılan ayarı kullanarak elde edilen performansın beklentiinizin altında olduğunu düşünüyorsanız, sonraki Test çalıştırmasında DIU ayarını daha büyük ölçüde artırabilirsiniz.

   DIU ayarını artırdıkça kopyalama etkinliğinin neredeyse kusursuz bir şekilde ölçeklendirilmesi gerekir.  DIU ayarı ikiye katlanarak üretilen iş öğesini görmüyorsanız iki şey meydana gelebilir:

   - Çalıştırmakta olduğunuz belirli bir kopya, daha fazla mus eklemekten yararlanmaz.  Daha büyük bir DIU değeri belirtseniz de, kullanılan gerçek DIU aynı şekilde kaldığı için, aynı aktarım hızını daha önce olduğu gibi elde edersiniz.  Bu durumda, 3. adım ile aynı anda birden fazla kopya çalıştırarak toplam aktarım hızını en üst düzeye çıkarın.
   - Daha fazla bilgi (daha fazla Horsepower) ekleyerek ve bu sayede veri ayıklama, aktarım ve yükleme hızını daha yüksek bir şekilde sunarak kaynak veri deposu, aralarındaki ağ veya hedef veri deposu performans sorununa ulaştı ve muhtemelen kısıtlanıyor.  Bu durumda, üst sınırı yükseltmek için veri deposu yöneticinize veya ağ yöneticinize başvurmayı deneyin veya alternatif olarak, azaltma işlemi gerçekleşene kadar DIU ayarını azaltın.

   **Kopyalama etkinliği kendi kendine barındırılan bir Integration Runtime yürütülürse:**

   Tümleştirme çalışma zamanını barındırmak için veri deposunu barındıran sunucudan ayrı bir adanmış makine kullanmanızı öneririz.

   [Paralel kopyalama](#parallel-copy) ayarı için varsayılan değerlerle başlayın ve kendınden konak IR için tek bir düğüm kullanın.  Bir performans testi çalıştırması gerçekleştirin ve elde edilen performansı bir yere göz atın.

   Daha yüksek aktarım hızı elde etmek isterseniz, şirket içinde barındırılan IR 'yi ölçeklendirebilir veya ölçeklendirebilirsiniz:

   - Şirket içinde barındırılan IR düğümündeki CPU ve kullanılabilir bellek tam olarak kullanılmaz, ancak eşzamanlı işlerin yürütülmesi sınıra ulaştığı takdirde, bir düğümde çalışabilecek eşzamanlı işlerin sayısını artırarak ölçeklendirmelisiniz.  Yönergeler için [buraya](create-self-hosted-integration-runtime.md#scale-up) bakın.
   - Diğer taraftan, CPU şirket içinde barındırılan IR düğümünde yüksek veya kullanılabilir bellek düşükse, birden çok düğümde yükün ölçeğini genişletmek için yeni bir düğüm ekleyebilirsiniz.  Yönergeler için [buraya](create-self-hosted-integration-runtime.md#high-availability-and-scalability) bakın.

   Şirket içinde barındırılan IR kapasitesini ölçeklendirdiğiniz veya ölçeklendirdikçe, giderek daha iyi üretilen iş elde ediyorsanız, performans testi çalıştırmasını tekrarlayın.  Aktarım hızı, büyük olasılıkla kaynak veri deposu, aralarındaki ağ veya hedef veri deposu performans sorununa ulaştı ve kısıtlanacak şekilde başlıyor. Bu durumda, üst sınırı yükseltmek için veri deposu yöneticinize veya ağ yöneticinize başvurmayı deneyin veya alternatif olarak, şirket içinde barındırılan IR için önceki ölçeklendirme ayarınıza geri dönün. 

3. **Birden çok kopyayı eşzamanlı olarak çalıştırarak toplam aktarım hızını en iyi duruma getirme:**

   Tek bir kopyalama etkinliğinin performansını zaten kapladığınıza göre, ortamınız-ağ, kaynak veri deposu ve hedef veri deposu için üretilen iş üst limitlerine henüz ulaşırsanız, ADF kullanarak birden çok kopyalama etkinliğini paralel olarak çalıştırabilirsiniz [her döngü için](control-flow-for-each-activity.md)gibi denetim akışı yapıları.

4. **Performans ayarlama ipuçları ve iyileştirme özellikleri.** Bazı durumlarda, Azure Data Factory bir kopyalama etkinliği çalıştırdığınızda, aşağıdaki örnekte gösterildiği gibi [kopyalama etkinliği izlemenin](copy-activity-overview.md#monitor-visually)üstünde "performans ayarlama ipuçları" iletisini görürsünüz. İleti size verilen kopya çalıştırması için tanımlanan performans sorunlarını söyler. Ayrıca, kopyalama aktarım hızını artırmak için nelerin değiştirileceği konusunda size rehberlik eder. Performans ayarlama ipuçları şu anda şu şekilde öneriler sunmaktadır:

   - Azure SQL veri ambarı 'na veri kopyaladığınızda PolyBase kullanın.
   - Veri deposu tarafındaki kaynak performans sorunu olduğunda Azure Cosmos DB Istek birimlerini veya Azure SQL veritabanı DTU 'ları (veritabanı Işleme birimleri) artırın.
   - Gereksiz hazırlanmış kopyayı kaldırın.

   Performans ayarlama kuralları da kademeli olarak zenginleşmelidir.

   **Örnek: performans ayarlama ipuçlarıyla Azure SQL veritabanına kopyalama**

   Bu örnekte, bir kopya çalıştırması sırasında, Azure SQL veritabanı 'nın, yazma işlemlerini yavaşlatan yüksek DTU kullanımına ulaştığını Azure Data Factory bildirimler. Öneri, Azure SQL veritabanı katmanını daha fazla DTU ile artırmaya yönelik bir öneridir. 

   ![Performansı ayarlama ipuçlarıyla izleme kopyalama](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   Ayrıca, aşağıdakiler göz önünde bulundurulması gereken bazı performans iyileştirme özellikleridir:

   - [Paralel kopya](#parallel-copy)
   - [Veri Tümleştirme Birimleri](#data-integration-units)
   - [Hazırlanmış kopya](#staged-copy)
   - [Şirket içinde barındırılan tümleştirme çalışma zamanı ölçeklenebilirliği](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **Yapılandırmayı tüm veri kümeniz için genişletin.** Yürütme sonuçları ve performansından memnun olduğunuzda, tüm veri kümenizi kapsayacak şekilde tanımı ve işlem hattını genişletebilirsiniz.

## <a name="copy-performance-optimization-features"></a>Performansı en iyi duruma getirme özelliklerini Kopyala

Azure Data Factory aşağıdaki performans iyileştirme özelliklerini sağlar:

- [Paralel kopya](#parallel-copy)
- [Veri Tümleştirme Birimleri](#data-integration-units)
- [Hazırlanmış kopya](#staged-copy)

### <a name="data-integration-units"></a>Veri tümleştirme birimleri

Veri tümleştirme birimi, Azure Data Factory içinde tek bir birimin gücünü (CPU, bellek ve ağ kaynak ayırma birleşimi) temsil eden bir ölçüdür. Veri tümleştirme birimi yalnızca [Azure tümleştirme çalışma zamanı](concepts-integration-runtime.md#azure-integration-runtime)için geçerlidir, ancak [Şirket içinde barındırılan tümleştirme çalışma zamanı](concepts-integration-runtime.md#self-hosted-integration-runtime)için geçerli değildir.

**Kullanılan mus \* kopyalama süresi \* birim fiyatı/DIU-saat**olarak ücretlendirilirsiniz. Geçerli fiyatlara [buradan](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)bakın. Yerel para birimi ve ayrı ayırt sayma, abonelik türü başına uygulanabilir.

Kopyalama etkinliği çalıştırmasını güçlendiren izin verilen, **2 ile 256 arasındadır**. Belirtilmezse veya Kullanıcı arabiriminde "otomatik" seçeneğini belirlerseniz, kaynak havuzu çiftine ve veri düzenine göre en iyi DIU ayarını dinamik olarak uygulayın Data Factory. Aşağıdaki tabloda, farklı kopyalama senaryolarında kullanılan varsayılan değer listelenmiştir:

| Kopyalama senaryosu | Hizmete göre belirlenen varsayılan değer |
|:--- |:--- |
| Dosya tabanlı mağazalar arasında veri kopyalama | Dosyaların sayısına ve boyutuna bağlı olarak 4 ile 32 arasında |
| Verileri Azure SQL veritabanı 'na veya Azure Cosmos DB kopyalama |Azure SQL veritabanı 'nın veya Cosmos DB katmanına bağlı olarak 4 ile 16 arasında (DTU sayısı/ru) |
| Diğer tüm kopyalama senaryoları | 4 |

Bu varsayılanı geçersiz kılmak için, **Dataıntegrationunits** özelliği için aşağıdaki gibi bir değer belirtin. Kopyalama işleminin çalışma zamanında kullandığı *gerçek sayı* , veri düzenlerinize bağlı olarak yapılandırılan değere eşit veya ondan daha az.

Bir etkinlik çalıştırmasını izlerken kopyalama etkinliği çıkışında her bir kopya için kullanılan orus 'yi görebilirsiniz. Daha fazla bilgi için bkz. [kopyalama etkinliği izleme](copy-activity-overview.md#monitoring).

> [!NOTE]
> Dörtten daha büyük bir ayar, şu anda yalnızca birden fazla dosyayı Azure Blob/ADLS 1./ADLS 2./Amazon S3/Google bulut depolama/bulut FTP/Cloud SFTP 'den veya bölüm-seçenek etkinleştirilmiş bulut ilişkisel veri deposundan (Oracle dahil) kopyaladığınızda geçerlidir. [ ](connector-oracle.md#oracle-as-source)diğer tüm bulut veri depolarına/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)).

**Örnek:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="parallel-copy"></a>Paralel kopya

Kopyalama etkinliğinin kullanmasını istediğiniz paralellik belirtmek için **Parallelkopyaları** özelliğini kullanabilirsiniz. Bu özelliği, kopyalama etkinliğindeki en fazla iş parçacığı sayısı olarak, kaynağınızdan okuyabilecek veya kanal veri depolarınız paralel olarak yazabileceğiniz şekilde düşünebilirsiniz.

Her kopyalama etkinliği çalıştırması için Azure Data Factory, verileri kaynak veri deposundan ve hedef veri deposuna kopyalamak için kullanılacak paralel kopya sayısını belirler. Varsayılan paralel kopya sayısı, kullandığınız kaynak ve havuz türüne bağlıdır.

| Kopyalama senaryosu | Hizmete göre belirlenen varsayılan paralel kopya sayısı |
| --- | --- |
| Dosya tabanlı mağazalar arasında veri kopyalama |Dosyaların boyutuna ve iki bulut veri deposu arasında veri kopyalamak için kullanılan DIUs sayısının yanı sıra şirket içinde barındırılan tümleştirme çalışma zamanı makinesinin fiziksel yapılandırması da bağlıdır. |
| Bölüm seçeneği etkinken ilişkisel veri deposundan kopyalama ( [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP tablosu](connector-sap-table.md#sap-table-as-source)ve [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)dahil)|4 |
| Tüm kaynak depolardan Azure Tablo depolamaya veri kopyalama |4 |
| Diğer tüm kopyalama senaryoları |1 |

> [!TIP]
> Dosya tabanlı mağazalar arasında veri kopyaladığınızda, varsayılan davranış genellikle en iyi performansı sağlar. Varsayılan davranış, kaynak dosya örüntüsünün temel alınarak otomatik olarak belirlenir.

Veri mağazalarınızı barındıran makinelerde yükü denetlemek veya kopyalama performansını ayarlamak için, varsayılan değeri geçersiz kılabilir ve **Parallelcopy** özelliği için bir değer belirtebilirsiniz. Değer 1 ' den büyük veya buna eşit bir tamsayı olmalıdır. Çalışma zamanında, en iyi performans için kopyalama etkinliği, ayarladığınız değerden küçük veya bu değere eşit bir değer kullanır.

**Şunlara işaret eder:**

- Dosya tabanlı mağazalar arasında veri kopyaladığınızda **Parallelcopy** , dosya düzeyinde paralellik belirler. Tek bir dosya içindeki parçalama otomatik ve şeffaf bir şekilde gerçekleşir. Verileri paralel ve **paralelde**paralel olarak yüklemek için belirli bir kaynak veri deposu türü için en uygun öbek boyutunu kullanmak üzere tasarlanmıştır. Çalışma zamanında kopyalama işlemi için veri taşıma hizmeti 'nin kullandığı paralel kopyaların gerçek sayısı sahip olduğunuz dosya sayısından daha fazla değil. Kopyalama davranışı **Mergefile**ise, kopyalama etkinliği dosya düzeyinde paralellik özelliğinden yararlanamaz.
- Dosya tabanlı olmayan mağazalardan ( [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP tablosu](connector-sap-table.md#sap-table-as-source)ve [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) Bağlayıcısı hariç) dosya tabanlı depolarda verileri kopyaladığınızda, bu veriler taşıma hizmeti **Parallelkopyaları** özelliğini yoksayar. Paralellik belirtilmiş olsa bile, bu durumda uygulanmaz.
- **Parallelkopyaların** özelliği **dataıntegrationunits**öğesine göre belirlenir. İlki tüm veri tümleştirme birimleri genelinde sayılır.
- **Parallelkopyaları** özelliği için bir değer belirttiğinizde, kaynak ve havuz Veri depolarındaki yük artışını göz önünde bulundurun. Ayrıca, kopyalama etkinliği, karma kopya için, bu, örneğin, karma kopyaya karşı güç alıyorsa, şirket içinde barındırılan tümleştirme çalışma zamanına yönelik yük artışını de göz önünde bulundurun. Bu yük artışı, özellikle aynı veri deposunda çalışan aynı etkinliklerin birden çok etkinliğiniz veya eş zamanlı çalıştırmaları olduğunda gerçekleşir. Veri deposunun veya şirket içinde barındırılan tümleştirme çalışma zamanının yük ile azaldığını fark ederseniz, yükü ortadan kaldırmak için **Parallelkopyaların** değerini azaltın.

**Örnek:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

### <a name="staged-copy"></a>Hazırlanmış kopya

Bir kaynak veri deposundan bir havuz veri deposuna veri kopyaladığınızda, blob Storage 'ı geçici bir hazırlama deposu olarak kullanmayı tercih edebilirsiniz. Hazırlama, özellikle aşağıdaki durumlarda yararlı olur:

- **PolyBase aracılığıyla çeşitli veri depolarındaki verileri SQL veri ambarı 'na almak istiyorsunuz.** SQL veri ambarı, çok büyük miktarda veriyi SQL veri ambarı 'na yüklemek için PolyBase 'i yüksek hacimli bir mekanizma olarak kullanır. Kaynak verilerin blob depolaması veya Azure Data Lake Store olması ve ek ölçütlere uyması gerekir. BLOB depolama veya Azure Data Lake Store dışında bir veri deposundan veri yüklediğinizde, veri kopyalamayı, geçici hazırlama blob depolaması aracılığıyla etkinleştirebilirsiniz. Bu durumda Azure Data Factory, PolyBase 'in gereksinimlerini karşıladığından emin olmak için gerekli veri dönüştürmelerini gerçekleştirir. Daha sonra, verileri SQL veri ambarı 'na verimli bir şekilde yüklemek için PolyBase 'i kullanır. Daha fazla bilgi için bkz. [PolyBase ile Azure SQL Veri Ambarı’na veri yükleme](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Bazen bir karma veri hareketini (yani, şirket içi veri deposundan bir bulut veri deposuna kopyalamak için) yavaş bir ağ bağlantısı üzerinden gerçekleştirme işlemi biraz zaman alır.** Performansı artırmak için, hazırlanan kopyayı, verileri bulutta hazırlama veri deposuna taşımak daha az zaman alması amacıyla Şirket içindeki verileri sıkıştırmak için kullanabilirsiniz. Daha sonra, hedef veri deposuna yüklemeden önce hazırlama deposundaki verileri açabilir.
- **Şirket BT ilkeleri nedeniyle güvenlik duvarınızdaki bağlantı noktası 80 ve bağlantı noktası 443 dışındaki bağlantı noktalarını açmak istemezsiniz.** Örneğin, şirket içi bir veri deposundan verileri bir Azure SQL veritabanı havuzuna veya Azure SQL veri ambarı havuzuna kopyaladığınızda, hem Windows Güvenlik Duvarı hem de kurumsal güvenlik duvarınız için bağlantı noktası 1433 ' de giden TCP iletişimini etkinleştirmeniz gerekir. Bu senaryoda, hazırlanan kopya, ilk olarak şirket içinde barındırılan tümleştirme çalışma zamanından yararlanarak, önce verileri HTTP veya 443 numaralı bağlantı noktasında HTTP veya HTTPS üzerinden BLOB depolama hazırlama örneğine kopyalayabilir. Ardından, BLOB depolama alanı hazırlamadaki verileri SQL veritabanı veya SQL veri ambarı 'na yükleyebilir. Bu akışta 1433 numaralı bağlantı noktasını etkinleştirmeniz gerekmez.

#### <a name="how-staged-copy-works"></a>Aşamalı kopya nasıl çalışacaktır?

Hazırlama özelliğini etkinleştirdiğinizde, önce veriler kaynak veri deposundan hazırlama BLOB depolama alanına kopyalanır (kendinizinkini getirin). Ardından, veriler hazırlama veri deposundan havuz veri deposuna kopyalanır. Azure Data Factory, sizin için iki aşamalı akışı otomatik olarak yönetir. Azure Data Factory, veri taşıma işlemi tamamlandıktan sonra hazırlama depolamadan geçici verileri de temizler.

![Hazırlanmış kopya](media/copy-activity-performance/staged-copy.png)

Veri hareketini bir hazırlama deposu kullanarak etkinleştirdiğinizde, verileri kaynak veri deposundan bir ara veya hazırlama veri deposuna taşımadan önce verilerin sıkıştırılıp sıkıştırılmayacağını ve sonra verileri bir ara veya hazırlama dat 'dan taşımadan önce sıkıştırması açılacak şekilde belirtebilirsiniz Havuz veri deposuna bir mağaza.

Şu anda, otomatik olarak barındırılan farklı IRS 'ler arasında bağlı olan iki veri deposu arasında veri kopyalayamazsınız, ancak hazırlanmamış kopya olmadan, verileri kopyalayamazsınız. Bu tür senaryolar için, kaynaktan hazırlama ve daha sonra hazırlama durumundan havuza kopyalamak üzere iki açık zincirleme kopyalama etkinliği yapılandırabilirsiniz.

#### <a name="configuration"></a>Yapılandırma

Hedef veri deposuna yüklemeden önce, verilerin blob depolamada hazırlanması isteyip istemediğinizi belirtmek için kopyalama etkinliğinde **Enablehazırlama** ayarını yapılandırın. **Enablehazırlama** `TRUE`olarak ayarladığınızda, aşağıdaki tabloda listelenen ek özellikleri belirtin. Ayrıca, yoksa, hazırlama için bir Azure depolama veya depolama paylaşılan erişim imzası ile bağlantılı hizmet oluşturmanız gerekir.

| Özellik | Açıklama | Varsayılan değer | Gerekli |
| --- | --- | --- | --- |
| Enablehazırlama |Verileri bir geçici hazırlama deposu aracılığıyla kopyalamak isteyip istemediğinizi belirtin. |False |Hayır |
| linkedServiceName |Geçici hazırlama deposu olarak kullandığınız depolama örneğine başvuran bir [Azurestorage](connector-azure-blob-storage.md#linked-service-properties) Linked hizmetinin adını belirtin. <br/><br/> PolyBase aracılığıyla SQL veri ambarı 'na veri yüklemek için, paylaşılan erişim imzasıyla depolama kullanamazsınız. Diğer tüm senaryolarda kullanabilirsiniz. |Yok |Evet, **Enablehazırlama** true olarak ayarlandığında |
| Yolun |Hazırlanan verileri içermesini istediğiniz BLOB depolama yolunu belirtin. Bir yol sağlamazsanız, hizmet geçici verileri depolamak için bir kapsayıcı oluşturur. <br/><br/> Yalnızca bir paylaşılan erişim imzasıyla depolama kullanırsanız veya geçici verilerin belirli bir konumda olmasını istiyorsanız bir yol belirtin. |Yok |Hayır |
| enableCompression |Verilerin hedefe kopyalanmadan önce sıkıştırılması gerekip gerekmediğini belirtir. Bu ayar, aktarılmakta olan verilerin hacmini azaltır. |False |Hayır |

>[!NOTE]
> Hazırlanan bir kopyayı sıkıştırma etkinken kullanırsanız, hazırlama blobu bağlı hizmeti için hizmet sorumlusu veya MSI kimlik doğrulaması desteklenmez.

Aşağıda, önceki tabloda açıklanan özelliklerle birlikte kopyalama etkinliğinin örnek bir tanımı verilmiştir:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

#### <a name="staged-copy-billing-impact"></a>Hazırlanan kopya faturalandırma etkisi

İki adım temelinde ücretlendirilirsiniz: kopyalama süresi ve kopya türü.

* Bulut kopyası sırasında hazırlama kullandığınızda, verileri bir bulut veri deposundan başka bir bulut veri deposuna kopyalar. her iki aşama de Azure tümleştirme çalışma zamanı tarafından güçlendirilirse, [1. adım ve 2. adım için kopyalama süresi toplamı] x [bulut kopyalama birimi fiyatı] ücretlendirirsiniz.
* Bir karma kopya sırasında hazırlama kullandığınızda, verileri şirket içi veri deposundan bir bulut veri deposuna kopyalayan bir aşama, kendinden konak bir tümleştirme çalışma zamanı tarafından güçlendirildiği için, [karma kopyalama süresi] x [karma kopya birim fiyatı] + [bulut kopyalama süresi] için ücret ödersiniz. x [bulut kopya birim fiyatı].

## <a name="references"></a>Başvurular

Desteklenen bazı veri depoları için performans izleme ve ayarlama başvuruları aşağıda verilmiştir:

* Blob depolamayı ve tablo depolamayı içeren Azure depolama: Azure depolama [ölçeklenebilirlik hedefleri](../storage/common/storage-scalability-targets.md) ve [Azure depolama performansı ve ölçeklenebilirlik denetim listesi](../storage/common/storage-performance-checklist.md).
* Azure SQL veritabanı: [performansı izleyebilir](../sql-database/sql-database-single-database-monitor.md) ve veritabanı işlem BIRIMI (DTU) yüzdesini kontrol edebilirsiniz.
* Azure SQL veri ambarı: yeteneği, veri ambarı birimlerinde (DWU) ölçülür. Bkz. [Azure SQL veri ambarı 'nda işlem gücünü yönetme (genel bakış)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Azure Cosmos DB performans düzeyleri](../cosmos-db/performance-levels.md).
* Şirket içi SQL Server: [performansı izleme ve ayarlama](https://msdn.microsoft.com/library/ms189081.aspx).
* Şirket içi dosya sunucusu: [dosya sunucuları Için performans ayarlama](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Sonraki adımlar
Diğer kopyalama etkinliği makalelerine bakın:

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Data Lake veya veri ambarınızdan verileri Azure 'a geçirmek için Azure Data Factory kullanın](data-migration-guidance-overview.md)
- [Amazon S3 'ten Azure Storage 'a veri geçirme](data-migration-guidance-s3-azure-storage.md)
