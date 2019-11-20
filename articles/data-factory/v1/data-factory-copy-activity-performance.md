---
title: Kopyalama etkinliği performansı ve ayarlama Kılavuzu
description: Kopyalama etkinliğini kullandığınızda Azure Data Factory veri hareketinin performansını etkileyen anahtar faktörleri hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c1cabbe3587079fa3fa5947eddbcf6cecaff3b98
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682912"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Kopyalama etkinliği performansı ve ayarlama Kılavuzu

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-copy-activity-performance.md)
> * [Sürüm 2 (geçerli sürüm)](../copy-activity-performance.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory için etkinlik performansını ve ayarlama kılavuzunu kopyalama](../copy-activity-performance.md).

Azure Data Factory kopyalama etkinliği, birinci sınıf güvenli, güvenilir ve yüksek performanslı bir veri yükleme çözümü sağlar. Her gün onlarca terabaytlık verileri birçok bulutta ve şirket içi veri depolarında kopyalamanızı sağlar. Hızlı veri yükleme performansı, çekirdek "büyük verileri" sorununa odaklanabilmeniz için önemli bir noktadır: gelişmiş analiz çözümleri oluşturma ve tüm bu verilerden derin Öngörüler alma.

Azure, kurumsal düzeyde veri depolama ve veri ambarı çözümleri kümesi sağlar ve kopyalama etkinliği, yapılandırılması ve ayarlanması kolay olan, yüksek oranda iyileştirilmiş bir veri yükleme deneyimi sunar. Yalnızca tek bir kopyalama etkinliğiyle şunları yapabilirsiniz:

* **1,2 Gbps**'de **Azure SQL veri ambarı** 'na veri yükleme. Kullanım örneği ile ilgili bir anlatım için, [Azure Data Factory ile 15 dakika altında 1 TB 'Yi Azure SQL veri ambarı 'Na yükleme](data-factory-load-sql-data-warehouse.md)bölümüne bakın.
* **1,0 Gbps** 'de **Azure Blob depolamaya** veri yükleme
* **Azure Data Lake Store** **1,0 Gbps** 'de veri yükleme

Bu makalede şunları açıklanmaktadır:

* Projenizin planlanmasına yardımcı olmak için desteklenen kaynak ve havuz veri depoları için [Performans başvuru numaraları](#performance-reference) ;
* [Bulut veri taşıma birimleri](#cloud-data-movement-units), [paralel kopyalama](#parallel-copy)ve [hazırlanan kopya](#staged-copy)dahil olmak üzere farklı senaryolarda kopyalama aktarım hızını arttıran özellikler;
* Performansı ve kopyalama performansını etkileyebilecek önemli faktörleri ayarlama hakkında [performans ayarlama Kılavuzu](#performance-tuning-steps) .

> [!NOTE]
> Kopyalama etkinliğini genel olarak bilmiyorsanız, bu makaleyi okumadan önce [kopyalama etkinliğini kullanarak verileri taşıma](data-factory-data-movement-activities.md) bölümüne bakın.
>

## <a name="performance-reference"></a>Performans başvurusu

Bir başvuru olarak, aşağıdaki tabloda, şirket içi test temelinde verilen kaynak ve havuz çiftleri için aktarım hızı sayısını MB/sn cinsinden gösterir. Karşılaştırma için aynı zamanda [bulut veri taşıma birimleri](#cloud-data-movement-units) veya [veri yönetimi ağ geçidi ölçeklenebilirliği](data-factory-data-management-gateway-high-availability-scalability.md) (birden çok ağ geçidi düğümleri) farklı ayarlarının, kopyalama performansı konusunda nasıl yardımcı olabileceğini gösterir.

![Performans matrisi](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Azure Data Factory sürüm 1 ' de, buluttan buluta kopyalama için en az bulut veri taşıma birimi ikdir. Belirtilmemişse, [bulut veri taşıma birimlerinde](#cloud-data-movement-units)kullanılan varsayılan veri taşıma birimleri bölümüne bakın.

**Şunlara işaret eder:**
* Aktarım hızı şu formül kullanılarak hesaplanır: [kaynaktan okunan veri boyutu]/[kopyalama etkinliği çalışma süresi].
* Tablodaki performans başvuru numaraları, tek bir kopyalama etkinliği çalıştırmasında, [TPC-H](http://www.tpc.org/tpch/) veri kümesi kullanılarak ölçülür.
* Azure veri depoları ' nda, kaynak ve havuz aynı Azure bölgesidir.
* Şirket içi ve bulut veri depoları arasındaki karma kopya için, her ağ geçidi düğümü, aşağıdaki belirtim ile şirket içi veri deposundan ayrı olan bir makinede çalışmaktadır. Ağ geçidinde tek bir etkinlik çalıştırılırken, kopyalama işlemi test makinesinin CPU, bellek veya ağ bant genişliğinin yalnızca küçük bir kısmını kullandı. [Veri yönetimi ağ geçidi için dikkat etmeniz gereken](#considerations-for-data-management-gateway)daha fazla bilgi edinin.
    <table>
    <tr>
        <td>CPU</td>
        <td>32 çekirdek 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Bellek</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Ağ</td>
        <td>Internet arabirimi: 10 Gbps; intranet arabirimi: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> Buluttan buluta kopyalama etkinliği çalıştırması için 32 olan varsayılan maksimum DMUs değerinden daha fazla veri taşıma birimi (DMU) kullanarak daha yüksek aktarım hızı elde edebilirsiniz. Örneğin, 100 DMUs ile, verileri Azure Blob 'tan **1,0 GB/sn**'ye kopyalamaya Azure Data Lake Store sağlayabilirsiniz. Bu özellik ve desteklenen senaryo hakkındaki ayrıntılar için [bulut verileri taşıma birimleri](#cloud-data-movement-units) bölümüne bakın. Daha fazla DMUs istemek için [Azure desteğine](https://azure.microsoft.com/support/) başvurun.

## <a name="parallel-copy"></a>Paralel kopya
Kaynaktan verileri okuyabilir veya **bir kopyalama etkinliği çalıştırmasında paralel olarak**hedefe veri yazabilirsiniz. Bu özellik bir kopyalama işleminin verimini geliştirir ve verilerin taşınması için geçen süreyi azaltır.

Bu ayar, etkinlik tanımındaki **eşzamanlılık** özelliğinden farklıdır. **Eşzamanlılık** özelliği, farklı etkinlik pencerelerinin verilerini işlemek Için **eşzamanlı kopyalama etkinliği** sayısını belirler (1, 2, 2, 3, 10:00, 3,-4, vb.). Bu özellik bir geçmiş yükü gerçekleştirdiğinizde faydalıdır. Paralel kopyalama özelliği **tek bir etkinlik çalıştırması**için geçerlidir.

Örnek senaryoya göz atalım. Aşağıdaki örnekte, geçmişteki bir çok dilimin işlenmesi gerekir. Data Factory her dilim için bir kopyalama etkinliği (etkinlik çalıştırması) örneği çalıştırır:

* İlk etkinlik penceresindeki (1 ' den 2 ' ye kadar) veri dilimi = = > etkinlik çalışması 1
* İkinci etkinlik penceresindeki veri dilimi (2-3 ' e kadar) = = > etkinlik çalışması 2
* İkinci etkinlik penceresindeki veri dilimi (3 ile 4 ' e kadar) = = > etkinlik çalıştırması 3

Etki alanları bu hiyerarşi sıralamasıyla devam eder.

Bu örnekte, **eşzamanlılık** değeri 2 olarak ayarlandığında, **etkinlik 1** ' i çalıştırın ve etkinlik, veri taşıma performansını geliştirmek için iki etkinlikten **aynı anda** **2** ' yi kopyalayın. Ancak, birden çok dosya etkinlik çalıştırması 1 ile ilişkiliyse, veri taşıma hizmeti dosyaları kaynaktan bir seferde hedef tek bir dosyaya kopyalar.

### <a name="cloud-data-movement-units"></a>Bulut veri taşıma birimleri
Bir **bulut veri taşıma birimi (DMU)** Data Factory içindeki tek bir birimin gücünü (CPU, bellek ve ağ kaynak ayırma birleşimi) temsil eden bir ölçüdür. DMU, karma kopyada değil, buluttan buluta kopyalama işlemleri için geçerlidir.

**Kopyalama etkinliği çalıştırmaya güç sağlamak için en az bulut veri taşıma birimleri iki.** Belirtilmezse, aşağıdaki tablo farklı kopyalama senaryolarında kullanılan varsayılan DMUs 'leri listeler:

| Kopyalama senaryosu | Hizmete göre belirlenen varsayılan DMUs |
|:--- |:--- |
| Dosya tabanlı mağazalar arasında veri kopyalama | 4 ile 16 arasında, dosyaların sayısına ve boyutuna bağlı olarak. |
| Diğer tüm kopyalama senaryoları | 4 |

Bu varsayılanı geçersiz kılmak için, **Clouddatamovementunits** özelliği için aşağıdaki gibi bir değer belirtin. **Clouddatamovementunits** özelliği için **izin verilen değerler** 2, 4, 8, 16, 32 ' dir. Kopyalama işleminin çalışma zamanında kullandığı **gerçek bulut DMU sayısı** , veri örünseçeneğe bağlı olarak yapılandırılan değere eşit veya ondan daha az. Belirli bir kopya kaynağı ve havuzu için daha fazla birim yapılandırırken alabileceğiniz performans kazancı düzeyi hakkında daha fazla bilgi için bkz. [Performans başvurusu](#performance-reference).

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

> [!NOTE]
> Daha yüksek bir verimlilik için daha fazla bulut DMUs gerekiyorsa [Azure desteği](https://azure.microsoft.com/support/)'ne başvurun. 8 ve üzeri ayarı şu anda yalnızca **BLOB depolama/Data Lake Store/Amazon S3/Cloud FTP/Cloud SFTP 'Den blob Storage/Data Lake Store/Azure SQL veritabanı 'na birden çok dosya kopyaladığınızda**geçerlidir.
>

### <a name="parallelcopies"></a>Paralellkopyalar
Kopyalama etkinliğinin kullanmasını istediğiniz paralellik belirtmek için **Parallelkopyalar** özelliğini kullanabilirsiniz. Bu özelliği, kaynağınızdan okunan veya paralel olarak havuz veri depolarına yazabilmesi için kopyalama etkinliği içindeki en fazla iş parçacığı sayısı olarak düşünebilirsiniz.

Her kopyalama etkinliği çalıştırması için Data Factory, verileri kaynak veri deposundan ve hedef veri deposuna kopyalamak için kullanılacak paralel kopya sayısını belirler. Varsayılan paralel kopya sayısı, kullanmakta olduğunuz kaynak ve havuz türüne bağlıdır.

| Kaynak ve havuz | Hizmete göre belirlenen varsayılan paralel kopya sayısı |
| --- | --- |
| Dosya tabanlı mağazalar (BLOB depolama;) arasında veri kopyalama Data Lake Store; Amazon S3; Şirket içi dosya sistemi; Şirket içi bir " |1 ile 32 arasında. , Verilerin boyutuna ve iki bulut veri deposu arasında veri kopyalamak için kullanılan bulut veri taşıma birimi (DMUs) sayısının veya karma kopya için kullanılan ağ geçidi makinesinin fiziksel yapılandırmasının (örneğin, şirket içi veri deposuna veri kopyalamak için) bağlı olarak değişir. |
| **Herhangi bir kaynak veri deposundan Azure Tablo depolama alanına** veri kopyalama |4 |
| Diğer tüm kaynak ve havuz çiftleri |1 |

Genellikle, varsayılan davranış size en iyi verimi vermelidir. Ancak, veri mağazalarınızı barındıran makinelerde yükü denetlemek veya kopyalama performansını ayarlamak için, varsayılan değeri geçersiz kılmayı ve **Parallelcopy** özelliği için bir değer belirtmeyi seçebilirsiniz. Değer 1 ile 32 (ikisi de dahil) arasında olmalıdır. Çalışma zamanında, en iyi performans için kopyalama etkinliği, ayarladığınız değerden küçük veya bu değere eşit bir değer kullanır.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Şunlara işaret eder:

* Dosya tabanlı mağazalar arasında veri kopyaladığınızda, **Paralellcopy** 'lar dosya düzeyinde paralellik belirlenir. Tek bir dosya içindeki parçalama otomatik ve şeffaf bir şekilde gerçekleşir ve verileri paralel ve paralelde Parallelkopyalarla yüklemek için belirli bir kaynak veri deposu türü için en uygun öbek boyutunu kullanmak üzere tasarlanmıştır. Çalışma zamanında kopyalama işlemi için veri taşıma hizmeti 'nin kullandığı paralel kopyaların gerçek sayısı sahip olduğunuz dosya sayısından daha fazla değil. Kopyalama davranışı **Mergefile**ise, kopyalama etkinliği dosya düzeyinde paralellik özelliğinden yararlanamaz.
* **Parallelcopy** özelliği için bir değer belirttiğinizde, kaynak ve havuz Veri depolarındaki yük artışını ve karma kopya ise ağ geçidini göz önünde bulundurun. Bu durum özellikle aynı veri deposunda çalışan aynı etkinliklerin birden çok aktiviteniz ya da eş zamanlı çalışma olduğunda gerçekleşir. Veri deposunun veya ağ geçidinin yük ile aşırı olduğunu fark ederseniz, yükü düşürmek için **Parallelkopyaların** değerini azaltın.
* Dosya tabanlı depolara dosya tabanlı olmayan mağazalardan veri kopyaladığınızda, veri taşıma hizmeti **Parallelcopy** özelliğini yoksayar. Paralellik belirtilmiş olsa bile, bu durumda uygulanmaz.

> [!NOTE]
> Karma kopya yaparken **Parallelcopy** özelliğini kullanmak Için veri yönetimi Gateway sürüm 1,11 veya üstünü kullanmanız gerekir.
>
>

Bu iki özelliği daha iyi kullanmak ve veri taşıma aktarım hızını geliştirmek için bkz. örnek kullanım örnekleri. Varsayılan davranışın avantajlarından yararlanmak için **Parallelkopyaları** yapılandırmanız gerekmez. Yapılandırma ve **Parallelkopyaları** çok küçükse, birden fazla bulut DMU tam olarak kullanılamayabilir.

### <a name="billing-impact"></a>Faturalama etkisi
Kopyalama işleminin toplam süresine göre ücretlendirildiğiniz **unutulmamalıdır.** Bir kopyalama işi tek bir bulut birimiyle bir saat geçmesi için kullanılırsa ve dört bulut birimi ile 15 dakika sürüyorsa, genel faturanız neredeyse aynı kalır. Örneğin, dört bulut birimi kullanırsınız. İlk bulut birimi 10 dakika, ikinci bir, 10 dakika, üçüncü bir, 5 dakika ve dört dakika, hepsi tek bir kopyalama etkinliğinde çalışır. 10 + 10 + 5 + 5 = 30 dakikalık toplam kopyalama (veri taşıma) zamanına göre ücretlendirilirsiniz. **Parallelkopyaları** kullanmak faturalandırmayı etkilemez.

## <a name="staged-copy"></a>Hazırlanmış kopya
Bir kaynak veri deposundan bir havuz veri deposuna veri kopyaladığınızda, blob Storage 'ı geçici bir hazırlama deposu olarak kullanmayı tercih edebilirsiniz. Hazırlama, özellikle aşağıdaki durumlarda yararlı olur:

1. **PolyBase aracılığıyla çeşitli veri depolarındaki VERILERI SQL veri ambarı 'na almak istiyorsunuz**. SQL veri ambarı, çok büyük miktarda veriyi SQL veri ambarı 'na yüklemek için PolyBase 'i yüksek hacimli bir mekanizma olarak kullanır. Ancak, kaynak verilerin BLOB depolama alanında olması ve ek ölçütlere uyması gerekir. BLOB depolama dışında bir veri deposundan veri yüklediğinizde, veri kopyalamayı, geçici hazırlama blob depolaması aracılığıyla etkinleştirebilirsiniz. Bu durumda Data Factory, PolyBase 'in gereksinimlerini karşıladığından emin olmak için gerekli veri dönüştürmelerini gerçekleştirir. Daha sonra SQL veri ambarı 'na veri yüklemek için PolyBase 'i kullanır. Daha ayrıntılı bilgi için bkz. [Azure SQL veri ambarı 'na veri yüklemek Için PolyBase kullanma](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Kullanım örneği ile ilgili bir anlatım için, [Azure Data Factory ile 15 dakika altında 1 TB 'Yi Azure SQL veri ambarı 'Na yükleme](data-factory-load-sql-data-warehouse.md)bölümüne bakın.
2. **Bazen bir karma veri hareketini (yani, şirket içi veri deposu ile bulut veri deposu arasında kopyalamak için) yavaş bir ağ bağlantısı üzerinden gerçekleştirme işlemi biraz zaman alır**. Performansı artırmak için Şirket içindeki verileri sıkıştırarak verileri bulutta hazırlama veri deposuna taşımak daha az zaman alır. Daha sonra, hedef veri deposuna yüklemeden önce hazırlama deposundaki verileri açabilir.
3. **ŞIRKET BT ilkeleri nedeniyle, güvenlik duvarınızdaki bağlantı noktası 80 ve bağlantı noktası 443 dışındaki bağlantı noktalarını açmak**istemezsiniz. Örneğin, şirket içi bir veri deposundan verileri bir Azure SQL veritabanı havuzuna veya Azure SQL veri ambarı havuzuna kopyaladığınızda, hem Windows Güvenlik Duvarı hem de kurumsal güvenlik duvarınız için bağlantı noktası 1433 ' de giden TCP iletişimini etkinleştirmeniz gerekir. Bu senaryoda, bağlantı noktası 443 ' de HTTP veya HTTPS üzerinden bir BLOB depolama hazırlama örneğine ilk olarak veri kopyalamak için ağ geçidinin avantajlarından yararlanın. Daha sonra, blob Storage hazırlama işleminden verileri SQL veritabanı veya SQL veri ambarı 'na yükleyin. Bu akışta 1433 numaralı bağlantı noktasını etkinleştirmeniz gerekmez.

### <a name="how-staged-copy-works"></a>Aşamalı kopya nasıl çalışacaktır?
Hazırlama özelliğini etkinleştirdiğinizde, önce veriler kaynak veri deposundan hazırlama veri deposuna kopyalanır (kendinizinkini getirin). Ardından, veriler hazırlama veri deposundan havuz veri deposuna kopyalanır. Data Factory, sizin için iki aşamalı akışı otomatik olarak yönetir. Data Factory, veri taşıma işlemi tamamlandıktan sonra hazırlama depolamadan geçici verileri de temizler.

Bulut kopyalama senaryosunda (hem kaynak hem de havuz veri depoları bulutta bulunur) ağ geçidi kullanılmaz. Data Factory hizmeti kopyalama işlemlerini gerçekleştirir.

![Hazırlanan kopya: bulut senaryosu](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

Karma kopyalama senaryosunda (kaynak şirket içi ve havuz bulutta ise) ağ geçidi, verileri kaynak veri deposundan hazırlama veri deposuna taşıdır. Data Factory hizmeti verileri hazırlama veri deposundan havuz veri deposuna taşıırın. Bir bulut veri deposundan verileri hazırlama yoluyla şirket içi veri deposuna kopyalama, tersine çevrilmiş akışta de desteklenir.

![Hazırlanan kopya: karma senaryo](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Veri hareketini bir hazırlama deposu kullanarak etkinleştirdiğinizde, verileri kaynak veri deposundan bir ara veya hazırlama veri deposuna taşımadan önce verilerin sıkıştırılıp sıkıştırılmayacağını ve sonra verileri bir geçici veya hazırlama verilerinden taşımadan önce sıkıştırması açılacak şekilde öğesini belirtebilirsiniz Havuz veri deposuna depolayın.

Şu anda, bir hazırlama deposu kullanarak iki şirket içi veri deposu arasında veri kopyalayamazsınız. Bu seçeneğin yakında kullanılabilir olmasını bekler.

### <a name="configuration"></a>Yapılandırma
Hedef veri deposuna yüklemeden önce verilerin blob depolamada hazırlanıp hazırlanmayacağını belirtmek için kopyalama etkinliğinde **Enablehazırlama** ayarını yapılandırın. **Enablehazırlama** özelliğini true olarak belirlediğinizde, sonraki tabloda listelenen ek özellikleri belirtin. Bir tane yoksa, hazırlama için bir Azure depolama veya depolama paylaşılan erişim imzası ile bağlantılı hizmet oluşturmanız da gerekir.

| Özellik | Açıklama | Varsayılan değer | Gerekli |
| --- | --- | --- | --- |
| **Enablehazırlama** |Verileri bir geçici hazırlama deposu aracılığıyla kopyalamak isteyip istemediğinizi belirtin. |False |Hayır |
| **linkedServiceName** |Bir [Azurestorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) veya [Azurestokıgesas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) bağlı hizmetinin adını belirtin ve bu, geçici hazırlama deposu olarak kullandığınız depolamanın örneğine başvurur. <br/><br/> PolyBase aracılığıyla SQL veri ambarı 'na veri yüklemek için paylaşılan erişim imzasıyla depolama kullanamazsınız. Diğer tüm senaryolarda kullanabilirsiniz. |Yok |Evet, **Enablehazırlama** true olarak ayarlandığında |
| **Yolun** |Hazırlanan verileri içermesini istediğiniz BLOB depolama yolunu belirtin. Bir yol sağlamazsanız, hizmet geçici verileri depolamak için bir kapsayıcı oluşturur. <br/><br/> Yalnızca bir paylaşılan erişim imzasıyla depolama kullanırsanız veya geçici verilerin belirli bir konumda olmasını istiyorsanız bir yol belirtin. |Yok |Hayır |
| **enableCompression** |Verilerin hedefe kopyalanmadan önce sıkıştırılması gerekip gerekmediğini belirtir. Bu ayar, aktarılmakta olan verilerin hacmini azaltır. |False |Hayır |

Aşağıda, önceki tabloda açıklanan özelliklerle birlikte kopyalama etkinliğinin örnek bir tanımı verilmiştir:

```json
"activities":[
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Faturalama etkisi
İki adım temelinde ücretlendirilirsiniz: kopyalama süresi ve kopya türü.

* Bir bulut kopyası sırasında hazırlama (bir bulut veri deposundan başka bir bulut veri deposuna veri kopyalama) kullandığınızda, [1. adım için kopyalama süresi toplamı ve 2. adım] x [bulut kopya birim fiyatı] ücretlendirilirsiniz.
* Karma kopya sırasında hazırlama kullandığınızda (verileri şirket içi veri deposundan bir bulut veri deposuna kopyalama), [karma kopyalama süresi] x [karma kopya birim fiyatı] + [bulut kopyalama süresi] x [bulut kopyalama birimi fiyatı] için ücretlendirilirsiniz.

## <a name="performance-tuning-steps"></a>Performans ayarlama adımları
Kopyalama etkinliğiyle Data Factory hizmetinizin performansını ayarlamak için şu adımları gerçekleştirmeniz önerilir:

1. **Taban çizgisi oluşturun**. Geliştirme aşamasında, bir temsili veri örneğine göre kopyalama etkinliğini kullanarak işlem hattınızı test edin. Çalıştığınız veri miktarını sınırlandırmak için Data Factory [Dilimleme modelini](data-factory-scheduling-and-execution.md) kullanabilirsiniz.

   **İzleme ve yönetim uygulamasını**kullanarak yürütme süresi ve performans özelliklerini toplayın. Data Factory giriş sayfanızda **izleyici & Yönet** ' i seçin. Ağaç görünümünde **Çıkış veri kümesini**seçin. **Etkinlik pencereleri** listesinde kopyalama etkinliği Çalıştır ' ı seçin. **Etkinlik pencereleri** kopyalama etkinlik süresini ve kopyalandığı verilerin boyutunu listeler. Verimlilik, **etkinlik penceresi Gezgini**' nde listelenir. Uygulama hakkında daha fazla bilgi edinmek için bkz. [izleme ve yönetim uygulamasını kullanarak Azure Data Factory işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md).

   ![Etkinlik çalışma ayrıntıları](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Makalenin ilerleyen kısımlarında, senaryolarınızın performansını ve yapılandırmasını, etkinliğimizden etkinliğin [performans başvurusunu](#performance-reference) kopyalamak için karşılaştırabilirsiniz.
2. **Performansı tanılayın ve iyileştirin**. Gözlemlediğiniz performans beklentilerinizi karşılamıyorsa, performans sorunlarını belirlemeniz gerekir. Daha sonra, performans sorunlarının etkisini kaldırmak veya azaltmak için performansı iyileştirin. Performans Tanılama 'nın tam açıklaması Bu makalenin kapsamı dışındadır, ancak bazı yaygın hususlar aşağıda verilmiştir:

   * Performans özellikleri:
     * [Paralel kopya](#parallel-copy)
     * [Bulut veri taşıma birimleri](#cloud-data-movement-units)
     * [Hazırlanmış kopya](#staged-copy)
     * [Veri Yönetimi ağ geçidi ölçeklenebilirliği](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Veri Yönetimi Ağ Geçidi](#considerations-for-data-management-gateway)
   * [Kaynak](#considerations-for-the-source)
   * [Ev](#considerations-for-the-sink)
   * [Serileştirme ve seri durumundan çıkarma](#considerations-for-serialization-and-deserialization)
   * [Masıyla](#considerations-for-compression)
   * [Sütun eşleme](#considerations-for-column-mapping)
   * [Diğer konular](#other-considerations)
3. **Yapılandırmayı tüm veri kümesi Için genişletin**. Yürütme sonuçları ve performansından memnun olduğunuzda, tüm veri kümesini kapsayacak şekilde tanım ve işlem hattı etkin dönemini genişletebilirsiniz.

## <a name="considerations-for-data-management-gateway"></a>Veri Yönetimi ağ geçidi ile ilgili konular
**Ağ geçidi kurulumu**: veri yönetimi ağ geçidini barındırmak için adanmış bir makine kullanmanızı öneririz. [Veri yönetimi ağ geçidini kullanma konularına göz](data-factory-data-management-gateway.md#considerations-for-using-gateway)atın.

**Ağ geçidi izleme ve ölçeği artırma/genişletme**: bir veya daha fazla ağ geçidi düğümü içeren tek bir mantıksal ağ geçidi aynı anda birden çok kopyalama etkinliği çalışmasına izin verebilir. Kaynak kullanımının neredeyse gerçek zamanlı anlık görüntüsünü (CPU, bellek, ağ (gelen/giden), vb.) bir ağ geçidi makinesi üzerinde ve Azure portal sınırı ile çalışan eşzamanlı işlerin sayısını görüntüleyebilirsiniz. bkz. [portalda ağ geçidini izleme](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Çok sayıda eşzamanlı kopyalama etkinliği çalıştırması veya kopyalamak için büyük miktarda veri içeren karma veri hareketi hakkında ağır gereksiniminiz varsa, kaynağınızı daha iyi kullanmak veya daha fazla kaynak sağlamak için [ağ geçidini ölçeklendirmeye veya ölçeklendirmeye](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) göz önünde bulundurun. kopya.

## <a name="considerations-for-the-source"></a>Kaynakla ilgili konular
### <a name="general"></a>Genel
Temel alınan veri deposunun üzerinde veya üzerinde çalışan diğer iş yükleri tarafından aşırı olmadığından emin olun.

Microsoft veri depoları için bkz. veri depolarına özgü [konuları izleme ve ayarlama](#performance-reference) , veri deposu performans özelliklerini anlamanıza, yanıt sürelerini en aza indirmenize ve üretilen işi en üst düzeye çıkarmaya yardımcı olma.

Blob depolamadaki verileri SQL veri ambarı 'na kopyalarsanız, performansı artırmak için **PolyBase** kullanmayı düşünün. Ayrıntılar için bkz. [PolyBase 'ı kullanarak Azure SQL veri ambarı 'na veri yükleme](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) . Kullanım örneği ile ilgili bir anlatım için, [Azure Data Factory ile 15 dakika altında 1 TB 'Yi Azure SQL veri ambarı 'Na yükleme](data-factory-load-sql-data-warehouse.md)bölümüne bakın.

### <a name="file-based-data-stores"></a>Dosya tabanlı veri depoları
*(Blob Storage, Data Lake Store, Amazon S3, şirket içi dosya sistemleri ve şirket içi olarak) dahildir*

* **Ortalama dosya boyutu ve dosya sayısı**: kopyalama etkinliği verileri tek seferde bir dosyaya aktarır. Taşınacak verilerin miktarı aynı olduğunda, veriler her bir dosyanın önyükleme aşaması nedeniyle birkaç küçük dosya yerine birçok küçük dosyadan oluşuyorsa genel aktarım hızı düşüktür. Bu nedenle, mümkünse daha yüksek aktarım hızı kazanmak için küçük dosyaları daha büyük dosyalara birleştirin.
* **Dosya biçimi ve sıkıştırma**: performansı geliştirmenin daha fazla yolu için, [serileştirme ve seri durumdan çıkarma](#considerations-for-serialization-and-deserialization) ve sıkıştırma bölümlerine [dikkat](#considerations-for-compression) edilmesi gerekenler bölümüne bakın.
* **Veri yönetimi ağ geçidinin** gerekli olduğu **Şirket içi dosya sistemi** senaryosu Için, [veri yönetimi ağ geçidine yönelik hususlar](#considerations-for-data-management-gateway) bölümüne bakın.

### <a name="relational-data-stores"></a>İlişkisel veri depoları
*(SQL veritabanı dahildir; SQL veri ambarı; Amazon Redshift; SQL Server veritabanları; ve Oracle, MySQL, DB2, Teradata, Sybase ve PostgreSQL veritabanları, vb.)*

* **Veri düzeni**: tablo şemanız kopyalama aktarım hızını etkiler. Büyük bir satır boyutu, aynı miktarda veriyi kopyalamak için küçük satır boyutundan daha iyi bir performans sağlar. Bunun nedeni, veritabanının daha az satır içeren daha az sayıda veriyi daha verimli bir şekilde almasına neden olabilir.
* **Sorgu veya saklı yordam**: verileri daha verimli bir şekilde getirmek Için kopyalama etkinlik kaynağında belirttiğiniz sorgu veya saklı yordamın mantığını iyileştirin.
* **Veri yönetimi ağ geçidinin**kullanımını gerektiren SQL Server ve Oracle gibi **Şirket içi ilişkisel veritabanları**için veri yönetimi ağ geçidine yönelik Konular bölümüne bakın.

## <a name="considerations-for-the-sink"></a>Havuz konuları
### <a name="general"></a>Genel
Temel alınan veri deposunun üzerinde veya üzerinde çalışan diğer iş yükleri tarafından aşırı olmadığından emin olun.

Microsoft veri depoları için, veri depolarına özgü [izleme ve ayarlama konularına](#performance-reference) bakın. Bu konular veri deposunun performans özelliklerini anlamanıza ve yanıt sürelerini en aza indirmenize ve aktarım hızını en üst düzeye çıkarmanıza yardımcı olabilir.

**BLOB depolamadan** **SQL veri ambarı**'na veri kopyalıyorsanız, performansı artırmak için **PolyBase** kullanmayı düşünün. Ayrıntılar için bkz. [PolyBase 'ı kullanarak Azure SQL veri ambarı 'na veri yükleme](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) . Kullanım örneği ile ilgili bir anlatım için, [Azure Data Factory ile 15 dakika altında 1 TB 'Yi Azure SQL veri ambarı 'Na yükleme](data-factory-load-sql-data-warehouse.md)bölümüne bakın.

### <a name="file-based-data-stores"></a>Dosya tabanlı veri depoları
*(Blob Storage, Data Lake Store, Amazon S3, şirket içi dosya sistemleri ve şirket içi olarak) dahildir*

* **Kopyalama davranışı**: farklı bir dosya tabanlı veri deposundan veri kopyalarsanız, kopyalama etkinliğinin **copybehavior** özelliği aracılığıyla üç seçeneği vardır. Hiyerarşiyi, düzlik hiyerarşisini veya birleştirme dosyalarını korur. Hiyerarşinin korunması veya düzleştirilmesi çok az ya da hiç performans yüküne neden olur, ancak dosyaları birleştirmek performans yükünün artmasına neden olur.
* **Dosya biçimi ve sıkıştırma**: performansı geliştirmenin daha fazla yolu için [serileştirme ve seri durumdan çıkarma](#considerations-for-serialization-and-deserialization) ve [sıkıştırma bölümlerine dikkat](#considerations-for-compression) edilecek noktalara göz atın.
* **BLOB depolama**: Şu anda BLOB depolama, en iyileştirilmiş veri aktarımı ve aktarım hızı için yalnızca blok bloblarını destekler.
* **Veri yönetimi ağ geçidi**kullanımını gerektiren **Şirket içi dosya sistemleri** senaryolarında, [veri yönetimi ağ geçidine yönelik konular](#considerations-for-data-management-gateway) bölümüne bakın.

### <a name="relational-data-stores"></a>İlişkisel veri depoları
*(SQL veritabanı, SQL veri ambarı, SQL Server veritabanları ve Oracle veritabanları dahildir)*

* **Kopyalama davranışı**: **sqlsink**için ayarladığınız özelliklere bağlı olarak kopyalama etkinliği verileri hedef veritabanına farklı yollarla yazar.
  * Varsayılan olarak, veri taşıma hizmeti, en iyi performansı sağlayan ekleme moduna veri eklemek için toplu kopyalama API 'sini kullanır.
  * Havuzda bir saklı yordam yapılandırırsanız, veritabanı verileri toplu yükleme yerine bir seferde bir satıra uygular. Performans önemli ölçüde düşer. Veri kümesi büyükse, uygulanabiliyorsa, **Sqlwritercleanupscript** özelliğini kullanmaya geçmeyi düşünün.
  * Her bir kopyalama etkinliği için **Sqlwritercleanupscript** özelliğini yapılandırırsanız, hizmet betiği tetikler ve ardından verileri eklemek Için toplu kopyalama API 'sini kullanırsınız. Örneğin, en son verilerle tüm tablonun üzerine yazmak için, kaynaktan yeni verileri toplu yüklemeden önce tüm kayıtları silmek üzere bir betik belirtebilirsiniz.
* **Veri deseninin ve yığın boyutu**:
  * Tablo şemanız kopyalama aktarım hızını etkiler. Aynı miktarda veriyi kopyalamak için, büyük bir satır boyutu küçük bir satır boyutundan daha iyi performans sağlar çünkü veritabanı daha az miktarda veri daha verimli bir şekilde veri kaydedebilir.
  * Kopyalama etkinliği, bir dizi toplu iş halinde veri ekler. Bir toplu işteki satır sayısını **Writebatchsize** özelliğini kullanarak ayarlayabilirsiniz. Verilerinizde küçük satırlar varsa, daha düşük toplu iş yükü ve daha yüksek aktarım hızı için **Writebatchsize** özelliğini daha yüksek bir değerle ayarlayabilirsiniz. Verilerinizin satır boyutu büyükse, **Writebatchsize**öğesini artırdığınızda dikkatli olun. Yüksek bir değer, veritabanının aşırı yüklenmesi nedeniyle oluşan bir kopyalama hatasına yol açabilir.
* **Veri yönetimi ağ geçidinin**kullanımını gerektiren SQL Server ve Oracle gibi **Şirket içi ilişkisel veritabanları** için [veri yönetimi ağ geçidine yönelik konular](#considerations-for-data-management-gateway) bölümüne bakın.

### <a name="nosql-stores"></a>NoSQL depoları
*(Tablo depolamayı ve Azure Cosmos DB içerir)*

* **Tablo Depolaması**için:
  * **Bölüm**: araya eklemeli bölümlere veri yazmak performansı önemli ölçüde düşürür. Verilerin bir bölüme göre etkili bir şekilde eklenmesini sağlamak için kaynak verilerinizi bölüm anahtarına göre sıralayın veya verileri tek bir bölüme yazmak için mantığı ayarlayın.
* **Azure Cosmos DB**için:
  * **Toplu iş boyutu**: **writebatchsize** özelliği, belge oluşturmak için Azure Cosmos DB hizmetine paralel istek sayısını ayarlar. Azure Cosmos DB için daha fazla paralel istek gönderildiğinden **Writebatchsize** ' i artırdığınızda daha iyi performans sağlayabilirsiniz. Ancak, Azure Cosmos DB yazdığınızda azaltma için izleyin (hata iletisi "Istek hızı büyük olur"). Belge boyutu, belgelerdeki terim sayısı ve hedef koleksiyonun dizin oluşturma ilkesi de dahil olmak üzere çeşitli faktörler azalmasına neden olabilir. Daha yüksek kopya işleme sağlamak için daha iyi bir koleksiyon (örneğin, S3) kullanmayı düşünün.

## <a name="considerations-for-serialization-and-deserialization"></a>Serileştirme ve seri durumdan çıkarma konuları
Giriş veri kümesi veya çıkış veri kümesi bir dosya ise serileştirme ve seri durumdan çıkarma gerçekleşebilir. Desteklenen dosya biçimlerine ilişkin ayrıntılarla, kopyalama etkinliğine göre desteklenen [dosya ve sıkıştırma biçimlerine](data-factory-supported-file-and-compression-formats.md) bakın.

**Kopyalama davranışı**:

* Dosya tabanlı veri depoları arasında dosya kopyalama:
  * Hem giriş hem de çıkış veri kümeleri aynı veya dosya biçimi ayarlarına sahip olduğunda, veri taşıma hizmeti herhangi bir serileştirme veya serisini kaldırma olmadan bir ikili kopya yürütür. Kaynak ve havuz dosya biçimi ayarlarının birbirinden farklı olduğu senaryoya kıyasla daha yüksek bir aktarım hızı görürsünüz.
  * Giriş ve çıkış verilerinin her ikisi de Metin biçimindeyse ve yalnızca kodlama türü farklıysa, veri taşıma hizmeti yalnızca kodlama dönüştürmesi yapar. Serileştirme ve seri durumdan çıkarma yapmaz ve bu, bir ikili kopyaya kıyasla bazı performans ek yüküne neden olur.
  * Giriş ve çıkış veri kümelerinin her ikisi de sınırlayıcılar gibi farklı dosya biçimlerine veya farklı yapılandırmalara sahip olduğunda, veri taşıma hizmeti kaynak verileri akışa alma, dönüştürme ve daha sonra belirttiğiniz çıkış biçiminde seri hale getirir. Bu işlem, diğer senaryolara kıyasla daha önemli bir performans yüküne neden olur.
* Dosya tabanlı olmayan bir veri deposuna/konumundan dosya kopyaladığınızda (örneğin, dosya tabanlı bir mağazadan bir ilişkisel depoya), serileştirme veya seri kaldırma adımı gereklidir. Bu adım, performans açısından önemli bir yüke neden olur.

**Dosya biçimi**: seçtiğiniz dosya biçimi, kopyalama performansını etkileyebilir. Örneğin, avro meta verileri verilerle depolayan bir Compact ikili biçimdir. İşlem ve sorgulama için Hadoop ekosisteminde geniş kapsamlı desteğe sahiptir. Ancak, avro serileştirme ve seri durumundan çıkarma için daha pahalıdır, bu da metin biçimiyle karşılaştırıldığında daha düşük kopya üretilen iş elde edilir. İşlem akışı genelinde dosya biçimi seçimini yapın. Verilerin depolandığı form, kaynak veri depoları veya dış sistemlerden ayıklanarak başlayın; depolama, analitik işleme ve sorgulama için en iyi biçim; ve verilerin raporlama ve görselleştirme araçları için veri Reyonları 'na aktarılması gereken biçim. Bazen, genel analitik işlemi göz önünde bulundurmanız durumunda okuma ve yazma performansına yönelik bir dosya biçimi iyi bir seçenek olabilir.

## <a name="considerations-for-compression"></a>Sıkıştırma konuları
Giriş veya çıkış veri kümesi bir dosya ise, kopyalama etkinliğini hedefe veri yazarken sıkıştırma veya sıkıştırmayı gerçekleştirecek şekilde ayarlayabilirsiniz. Sıkıştırma seçeneğini belirlediğinizde giriş/çıkış (g/ç) ve CPU arasında bir zorunluluğunu getirir yaparsınız. İşlem kaynaklarında ekstra veri maliyetlerini sıkıştırma. Ancak döndürülen ağ g/ç ve depolamayı azaltır. Verilerinize bağlı olarak, genel kopya aktarım hızına yönelik bir artırma görebilirsiniz.

**Codec**: kopyalama etkinliği gzip, bzip2 ve söndür sıkıştırma türlerini destekler. Azure HDInsight, işlenmek üzere üç türü de kullanabilir. Her sıkıştırma codec bileşeni avantajlara sahiptir. Örneğin, bzip2 en düşük kopya aktarım hızına sahiptir, ancak işleme için bölünebileceğiniz için Bzip2 ile en iyi Hive sorgu performansını elde edersiniz. Gzip en dengeli seçenektir ve en sık kullanılır. Uçtan uca senaryonuza en uygun codec bileşeni seçin.

**Düzey**: her sıkıştırma codec bileşeni için iki seçenekten birini seçebilirsiniz: en hızlı sıkıştırılmış ve en iyi sıkıştırılmış. En hızlı sıkıştırılmış seçenek, elde edilen dosya en iyi şekilde sıkıştırılmaz olsa bile, verileri mümkün olduğunca çabuk sıkıştırır. En iyi şekilde sıkıştırılan seçenek, sıkıştırma sırasında daha fazla zaman harcamıştır ve en az miktarda veri verir. Her iki seçeneği de test edebilir ve bu durum, sizin için daha iyi genel performans sağlar.

**Göz**önünde bulundurun: şirket içi bir mağaza ve bulut arasında büyük miktarda veriyi kopyalamak için, sıkıştırma ile ara BLOB depolama kullanmayı düşünün. Şirket ağınızın ve Azure hizmetlerinizin bant genişliği sınırlayan faktör olduğunda ve giriş verileri kümesi ve çıktı verilerinin her ikisinin de sıkıştırılmamış biçimde olmasını istiyorsanız ara depolama kullanmak faydalıdır. Daha belirgin bir şekilde, tek bir kopyalama etkinliğini iki kopyalama etkinliğine kesebilirsiniz. İlk kopyalama etkinliği kaynaktan sıkıştırılmış biçimde bir ara veya hazırlama blobuna kopyalanır. İkinci kopyalama etkinliği, sıkıştırılmış verileri hazırlama işleminden kopyalar ve sonra havuza yazarken bu verileri açar.

## <a name="considerations-for-column-mapping"></a>Sütun eşleme konuları
Kopyalama etkinliğinde **ColumnMappings** özelliğini, giriş sütunlarının tümünü veya bir alt kümesini çıkış sütunlarına eşlenecek şekilde ayarlayabilirsiniz. Veri taşıma hizmeti kaynaktaki verileri okuduktan sonra, verileri havuza yazmadan önce verilerde sütun eşlemesi gerçekleştirmesi gerekir. Bu ek işlem, kopyalama aktarım hızını azaltır.

Kaynak veri depetinizi (örneğin, SQL veritabanı veya SQL Server gibi bir ilişkisel depodaysa veya tablo depolama ya da Azure Cosmos DB gibi bir NoSQL depoluk ise, **sorgu** özelliğine sütun filtreleme ve yeniden sıralama mantığını göndermeyi düşünün. Sütun eşlemeyi kullanmak yerine. Bu şekilde, veri taşıma hizmeti, kaynak veri deposundan verileri okurken ve çok daha verimli olduğu sürece projeksiyon oluşur.

## <a name="other-considerations"></a>Diğer konular
Kopyalamak istediğiniz verilerin boyutu büyükse, Data Factory Dilimleme mekanizmasını kullanarak verileri daha fazla bölümlemek için iş mantığınızı ayarlayabilirsiniz. Sonra, kopyalama etkinliğini, her kopyalama etkinliği çalışmasının veri boyutunu azaltmak için daha sık çalışacak şekilde zamanlayın.

Aynı anda aynı veri deposuna bağlayıcı Data Factory gerektiren veri kümeleri ve kopyalama etkinliklerinin sayısı hakkında dikkatli olun. Birçok eşzamanlı kopyalama işi bir veri deposunu kısıtlayabilir ve performansı azalmasına, iş iç yeniden denemelerini kopyalamaya ve bazı durumlarda yürütme hatalarına neden olabilirler.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Örnek senaryo: şirket içi SQL Server blob depolamaya kopyalama
**Senaryo**: bir işlem hattı, verileri şirket ıçı SQL Server CSV biçiminde blob depolamaya kopyalamak için oluşturulmuştur. Kopyalama işini daha hızlı hale getirmek için CSV dosyalarının bzip2 biçiminde sıkıştırılması gerekir.

**Test ve analiz**: kopyalama etkinliğinin verimlilik 2 Mbps 'den azdır, bu da performans kıyaslamasından çok daha yavaştır.

**Performans Analizi ve ayarlama**: performans sorununu gidermek için verilerin nasıl işlendiğini ve taşındığını inceleyelim.

1. **Verileri oku**: ağ geçidi, SQL Server bir bağlantı açar ve sorguyu gönderir. SQL Server, veri akışını intranet üzerinden ağ geçidine göndererek yanıt verir.
2. **Verileri serileştirme ve sıkıştırma**: ağ geçidi, VERI akışını CSV biçiminde serileştirir ve verileri bir bzip2 akışına sıkıştırır.
3. **Veri yazma**: ağ geçidi, bzip2 akışını Internet üzerinden blob depolamaya yükler.

Gördüğünüz gibi, veriler işlenir ve akış sıralı bir şekilde taşınır: SQL Server > LAN > Ağ Geçidi > WAN > BLOB depolama. **Genel performans, işlem hattı genelinde en düşük aktarım hızına göre işlenir**.

![Veri akışı](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Aşağıdaki faktörlerden biri veya birkaçı performans sorununa neden olabilir:

* **Kaynak**: SQL Server kendisi ağır yükler nedeniyle düşük aktarım hızına sahiptir.
* **Veri yönetimi ağ geçidi**:
  * **LAN**: Gateway, SQL Server makineden uzakta bulunur ve düşük bant genişliğine sahip bir bağlantıya sahiptir.
  * **Ağ geçidi**: ağ geçidi, aşağıdaki işlemleri gerçekleştirmek için yük kısıtlamalarına ulaştı:
    * Serileştirme: veri akışının CSV biçiminde **serileştirilmesi**, performansı düşürür.
    * **Sıkıştırma**: yavaş bir sıkıştırma codec 'i seçtiniz (örneğin, bzip2, Çekirdek i7 Ile 2,8 Mbps).
  * **WAN**: kurumsal ağ ile Azure hizmetleriniz arasındaki bant genişliği düşüktür (örneğin, T1 = 1.544 kbps; T2 = 6.312 kbps).
* **Havuz**: blob depolamada düşük aktarım hızı vardır. (Bu senaryo, SLA 'Sı en az 60 MBps 'Lik bir değer garanti ettiğinden düşüktür.)

Bu durumda, bzip2 veri sıkıştırması tüm işlem hattının yavaşlatıyor olabilir. Bir gzip sıkıştırma codec bileşenine geçiş yapmak bu sorunu kolaylaştırabilir.

## <a name="sample-scenarios-use-parallel-copy"></a>Örnek senaryolar: paralel kopya kullan
**Senaryo ı:** Şirket içi dosya sisteminden blob depolamaya 1.000 1-MB dosyalarını kopyalayın.

**Analiz ve performans ayarlaması**: Örneğin, bir dört çekirdekli makineye ağ geçidi yüklediyseniz, Data Factory dosyaları dosya sisteminden blob depolamaya eşzamanlı olarak taşımak için 16 paralel kopya kullanır. Bu paralel yürütme, yüksek verimlilik elde etmelidir. Ayrıca paralel kopya sayısını açıkça belirtebilirsiniz. Birçok küçük dosyayı kopyaladığınızda, paralel kopyalar kaynakları daha verimli bir şekilde kullanarak aktarım hızına önemli ölçüde yardımcı olur.

![Senaryo 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Senaryo II**: her biri Blob depolamadan Data Lake Store Analytics 'e 20 blob 500 MB kopyalayın ve ardından performansı ayarlayın.

**Analiz ve performans ayarlaması**: bu senaryoda Data Factory, tek kopya (**parallelcopy** 1 olarak ayarlanmış) ve tek bulut veri taşıma birimleri kullanarak verileri blob depolamadan Data Lake Store kopyalar. Gözettiğiniz aktarım hızı, [performans Başvurusu bölümünde](#performance-reference)açıklanacak şekilde kapatılacak.

![Senaryo 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Senaryo III**: tek dosya boyutu düzinelerce MB 'den büyük ve toplam birim büyük.

**Analiz ve performans açılıyor**: **parallelcopy** 'leri artırma, tek bir bulut DMU 'nin kaynak sınırlamaları nedeniyle daha iyi kopyalama performansına neden olmaz. Bunun yerine, veri hareketini gerçekleştirmeye yönelik daha fazla kaynak almak için daha fazla bulut DMU belirtmelisiniz. **Parallelkopyaların** özelliği için bir değer belirtmeyin. Data Factory, sizin için paralellik uygular. Bu durumda, **Clouddatamovementunits** 'i 4 olarak ayarlarsanız, yaklaşık dört kez üretilen iş meydana gelir.

![Senaryo 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Başvuru
Desteklenen bazı veri depoları için performans izleme ve ayarlama başvuruları aşağıda verilmiştir:

* Azure depolama (BLOB depolama ve tablo depolaması dahil): [Azure depolama ölçeklenebilirlik hedefleri](../../storage/common/storage-scalability-targets.md) ve [Azure depolama performansı ve ölçeklenebilirlik denetim listesi](../../storage/common/storage-performance-checklist.md)
* Azure SQL veritabanı: [performansı izleyebilir](../../sql-database/sql-database-single-database-monitor.md) ve veritabanı işlem BIRIMI (DTU) yüzdesini kontrol edebilirsiniz
* Azure SQL veri ambarı: yeteneği, veri ambarı birimlerinde (DWU) ölçülür. bkz. [Azure SQL veri ambarı 'nda işlem gücünü yönetme (genel bakış)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [Azure Cosmos DB performans düzeyleri](../../cosmos-db/performance-levels.md)
* Şirket içi SQL Server: [performansı izleme ve ayarlama](https://msdn.microsoft.com/library/ms189081.aspx)
* Şirket içi dosya sunucusu: [dosya sunucuları Için performans ayarlama](https://msdn.microsoft.com/library/dn567661.aspx)
