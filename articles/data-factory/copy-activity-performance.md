---
title: Kopyalama etkinliği performans ve ölçeklenebilirlik kılavuzu
description: Kopyalama etkinliğini kullandığınızda Azure Data Factory veri hareketinin performansını etkileyen anahtar faktörleri hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/15/2020
ms.openlocfilehash: 75f9080b43333168802a72e60751eec2a765c6d4
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580809"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Kopyalama etkinliği performans ve ölçeklenebilirlik kılavuzu

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Azure Data Factory sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-copy-activity-performance.md)
> * [Güncel sürüm](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bazen Data Lake veya kurumsal veri ambarından (EDW) Azure 'a büyük ölçekli veri geçişi gerçekleştirmek isteyebilirsiniz. Büyük veri analizi için, farklı kaynaklardan Azure 'a büyük miktarda veri almak istediğiniz diğer saatler. Her durumda, en iyi performans ve ölçeklenebilirlik elde etmek önemlidir.

Azure Data Factory (ADF), verileri almak için bir mekanizma sağlar. ADF 'nin avantajları şunlardır:

* Büyük miktarlarda veriyi işler
* Yüksek performanslı
* Ekonomik

Bu avantajlar, ADF 'yi yüksek performans olan ölçeklenebilir veri alma işlem hatları oluşturmak isteyen veri mühendislerinin mükemmel bir şekilde sığdırmasını sağlar.

Bu makaleyi okuduktan sonra aşağıdaki soruları cevaplayabilirsiniz:

* Veri taşıma ve veri alma senaryolarında ADF kopyalama etkinliğini kullanarak ne düzeyde performans ve ölçeklenebilirlik elde edebilirim?
* ADF kopyalama etkinliğinin performansını ayarlamak için hangi adımları gerçekleştirmeniz gerekir?
* Tek bir kopyalama etkinliğinin çalışması için performansı iyileştirmek üzere ADF performans iyileştirmesi her ne kadar kullanabilir?
* Kopyalama performansını en iyi duruma getirirken ADF dışında hangi diğer faktörler göz önüne

> [!NOTE]
> Kopyalama etkinliğini genel olarak bilmiyorsanız, bu makaleyi kullanmadan önce [kopyalama etkinliğine genel bakış](copy-activity-overview.md) bölümüne bakın.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>ADF kullanarak performans ve ölçeklenebilirlik ulaşılabilir kopyalama

ADF, farklı düzeylerde paralellik sağlayan sunucusuz bir mimari sağlar.

Bu mimari, ortamınız için veri taşıma aktarım hızını en üst düzeye çıkaran işlem hatları geliştirmenize olanak tanır. Bu işlem hatları aşağıdaki kaynakları tamamen kullanır:

* Kaynak ve hedef veri depoları arasında ağ bant genişliği
* Kaynak veya hedef veri deposu saniye başına giriş/çıkış işlemi (ıOPS) ve bant genişliği

Bu tam kullanım, aşağıdaki kaynaklarla kullanılabilen minimum aktarım hızını ölçerek genel aktarım hızını tahmin edebileceğiniz anlamına gelir:

* Kaynak veri deposu
* Hedef veri deposu
* Kaynak ve hedef veri depoları arasında ağ bant genişliği

Aşağıdaki tabloda, veri taşıma süresinin hesaplanması gösterilmektedir. Her hücredeki süre, belirli bir ağ ve veri deposu bant genişliği ve belirli bir veri yükü boyutu temel alınarak hesaplanır.

> [!NOTE]
> Aşağıda belirtilen süre, bir veya daha fazla eşzamanlı kopyalama etkinliğini bölümlemek için ForEach kullanma dahil olmak üzere, bir veya daha fazla eşzamanlı veri tümleştirme [çözümünde, ADF](#copy-performance-optimization-features)kullanılarak uygulanan bir veya daha fazla performans iyileştirmesi için ulaşılabilir performansını temsil etmek üzere tasarlanmıştır. Belirli veri kümeniz ve sistem yapılandırmanız için kopyalama performansını iyileştirmek üzere [performans ayarlama adımlarında](#performance-tuning-steps) oluşan adımları izlemeniz önerilir. Üretim dağıtım planlaması, kapasite planlama ve faturalandırma projeksiyonu için performans ayarlama testlerinizde elde edilen numaraları kullanmanız gerekir.

&nbsp;

| Veri boyutu/ <br/> bant genişliği | 50 Mb/sn    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 dk    | 1,4 dk   | 0,3 dk   | 0,1 dk  | 0,03 dk | 0,01 dk | 0,0 dk   |
| **10 GB**                   | 27,3 dk   | 13,7 dk  | 2,7 dk   | 1,3 dk  | 0,3 dk  | 0,1 dk  | 0,03 dk  |
| **100 GB**                  | 4,6 saat    | 2,3 saat   | 0,5 saat   | 0,2 saat  | 0,05 Saat | 0,02 Saat | 0,0 saat   |
| **1 TB**                    | 46,6 saat   | 23,3 saat  | 4,7 saat   | 2,3 saat  | 0,5 saat  | 0,2 saat  | 0,05 Saat  |
| **10 TB**                   | 19,4 gün  | 9,7 gün  | 1,9 gün  | 0,9 gün | 0,2 gün | 0,1 gün | 0,02 gün |
| **100 TB**                  | 194,2 gün | 97,1 gün | 19,4 gün | 9,7 gün | 1,9 gün | 1 gün    | 0,2 gün  |
| **1 PB**                    | 64,7 Mo    | 32,4 Mo   | 6,5 Mo    | 3,2 Mo   | 0,6 Mo   | 0,3 Mo   | 0,06 Mo   |
| **10 PB**                   | 647,3 Mo   | 323,6 Mo  | 64,7 Mo   | 31,6 Mo  | 6,5 Mo   | 3,2 Mo   | 0,6 Mo    |
| | |  | | |  | | |

ADF kopyası farklı düzeylerde ölçeklenebilir:

![ADF kopyalama nasıl ölçeklendirilir](media/copy-activity-performance/adf-copy-scalability.png)

* ADF denetim akışı, örneğin [her döngü için](control-flow-for-each-activity.md)kullanarak birden çok kopyalama etkinliğini paralel olarak başlatabilir.

* Tek bir kopyalama etkinliği, ölçeklenebilir işlem kaynaklarından yararlanabilir.
  * Azure Integration Runtime (IR) kullanırken, her kopyalama etkinliği için sunucusuz bir şekilde [en fazla 256 veri tümleştirme birimi (DIUs)](#data-integration-units) belirtebilirsiniz.
  * Şirket içinde barındırılan IR kullanırken aşağıdaki yaklaşımlardan birini alabilirsiniz:
    * Makineyi el ile ölçeklendirin.
    * Birden çok makineye ([4 düğüme kadar](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) ölçeklendirin ve tek bir kopyalama etkinliği, dosya kümesini tüm düğümlerde bölümleyebilir.

* Tek bir kopyalama etkinliği, [paralel olarak](#parallel-copy)birden çok iş parçacığı kullanarak veri deposundan okur ve yazar.

## <a name="performance-tuning-steps"></a>Performans ayarlama adımları

Kopyalama etkinliğiyle Azure Data Factory hizmetinizin performansını ayarlamak için aşağıdaki adımları uygulayın:

1. **Bir test veri kümesi seçin ve bir taban çizgisi oluşturun.**

    Geliştirme sırasında, bir temsili veri örneğine göre kopyalama etkinliğini kullanarak işlem hattınızı test edin. Seçtiğiniz veri kümesi, aşağıdaki öznitelikler doğrultusunda tipik veri desenlerinizi temsil etmelidir:

    * Klasör yapısı
    * Dosya kalıbı
    * Veri şeması

    Ve veri kümeniz, kopyalama performansını değerlendirmek için yeterince büyük olmalıdır. Kopyalama etkinliğinin tamamlanabilmesi için iyi bir boyut en az 10 dakika sürer. [Kopyalama etkinliği izlemeyi](copy-activity-monitoring.md)izleyen yürütme ayrıntılarını ve performans özelliklerini toplayın.

2. **Tek bir kopyalama etkinliğinin performansını en üst düzeye çıkarma**:

    İlk olarak tek bir kopyalama etkinliği kullanarak performansı en üst düzeye çıkarmanızı öneririz.

    * **Kopyalama etkinliği bir _Azure_ tümleştirme çalışma zamanı üzerinde yürütülürse:**

        [Veri tümleştirme birimleri (DIU)](#data-integration-units) ve [paralel kopya](#parallel-copy) ayarları için varsayılan değerlerle başlayın.

    * **Kopyalama etkinliği şirket içinde _barındırılan_ tümleştirme çalışma zamanı üzerinde yürütülürse:**

        IR barındırmak için adanmış bir makine kullanmanızı öneririz. Makinenin, veri deposunu barındıran sunucudan ayrı olması gerekir. [Paralel kopyalama](#parallel-copy) ayarı için varsayılan değerlerle başlayın ve kendınden konak IR için tek bir düğüm kullanın.

    Bir performans testi çalıştırması gerçekleştirin. Elde edilen performansa göz atın. , DIUs ve paralel kopyalar gibi kullanılan gerçek değerleri dahil edin. Kullanılan çalıştırma sonuçları ve performans ayarlarının nasıl toplanacağı hakkında [Etkinlik izlemeyi kopyalama](copy-activity-monitoring.md) konusuna bakın. Performans sorunlarının belirlenmesi ve çözümlenmesi için [kopyalama etkinliği performansını nasıl giderebileceğinizi](copy-activity-performance-troubleshooting.md) öğrenin.

    Sorun giderme ve ayarlama kılavuzlarından sonra ek performans testi çalıştırmaları yürütmek için yineleme yapın. Tek bir kopyalama etkinliği çalıştıktan sonra, birden çok kopyayı eşzamanlı olarak çalıştırarak toplam aktarım hızını en üst düzeye çıkarmayı düşünün. Bu seçenek, bir sonraki numaralandırılmış madde işaretiyle ele alınmıştır.

3. **Birden çok kopyayı eşzamanlı olarak çalıştırarak toplam aktarım hızını en iyi duruma getirme:**

    Artık tek bir kopyalama etkinliğinin performansını zaten kaplamıştır. Ortamınızın üretilen iş üst limitlerine henüz ulaşırsanız, birden çok kopyalama etkinliğini paralel olarak çalıştırabilirsiniz. ADF denetim akışı yapılarını kullanarak paralel olarak çalıştırabilirsiniz. Bu tür bir yapı [her döngü için](control-flow-for-each-activity.md). Daha fazla bilgi için, çözüm şablonları hakkında aşağıdaki makalelere bakın:

    * [Birden çok kapsayıcıdan dosyaları kopyalama](solution-template-copy-files-multiple-containers.md)
    * [Amazon S3 'ten ADLS 2. veri geçirme](solution-template-migration-s3-azure.md)
    * [Denetim tablosu ile toplu kopyalama](solution-template-bulk-copy-with-control-table.md)

4. **Yapılandırmayı tüm veri kümeniz için genişletin.**

    Yürütme sonuçları ve performansından memnun olduğunuzda, tüm veri kümenizi kapsayacak şekilde tanımı ve işlem hattını genişletebilirsiniz.

## <a name="troubleshoot-copy-activity-performance"></a>Kopyalama etkinliği performansını sorun giderme

Senaryolarınız için performans testi planlamak ve yürütmek için [performans ayarlama adımlarını](#performance-tuning-steps) izleyin. Ve [kopyalama etkinliği performansıyla Ilgili sorun giderme](copy-activity-performance-troubleshooting.md)Azure Data Factory içindeki her bir kopyalama etkinliği çalışmasının performans sorununu nasıl giderebileceğinizi öğrenin.

## <a name="copy-performance-optimization-features"></a>Performansı en iyi duruma getirme özelliklerini Kopyala

Azure Data Factory aşağıdaki performans iyileştirme özelliklerini sağlar:

* [Veri Tümleştirme Birimleri](#data-integration-units)
* [Şirket içinde barındırılan tümleştirme çalışma zamanı ölçeklenebilirliği](#self-hosted-integration-runtime-scalability)
* [Paralel kopya](#parallel-copy)
* [Hazırlanmış kopya](#staged-copy)

### <a name="data-integration-units"></a>Veri Tümleştirme Birimleri

Veri tümleştirme birimi (DIU), Azure Data Factory tek bir birimin gücünü temsil eden bir ölçüdür. Güç, CPU, bellek ve ağ kaynağı ayırmanın bir birleşimidir. DIU yalnızca [Azure tümleştirme çalışma zamanı](concepts-integration-runtime.md#azure-integration-runtime)için geçerlidir. DIU, [kendi kendine barındırılan tümleştirme çalışma zamanı](concepts-integration-runtime.md#self-hosted-integration-runtime)için uygulanmaz. [Burada daha fazla bilgi edinebilirsiniz](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı ölçeklenebilirliği

Artan bir eşzamanlı iş yükünü barındırmak isteyebilirsiniz. Ya da mevcut iş yükü düzeyinde daha yüksek performans elde etmek isteyebilirsiniz. Aşağıdaki yaklaşımlardan işleme ölçeğini geliştirebilirsiniz:

* Bir düğümde çalışabilecek [eşzamanlı işlerin](create-self-hosted-integration-runtime.md#scale-up) sayısını artırarak şirket IÇINDE barındırılan IR _'nin ölçeğini artırabilirsiniz_ .  
Ölçeği yalnızca, düğümün işlemcisi ve belleği tam olarak kullanılandan azsa çalışır.
* Daha fazla düğüm (makine) ekleyerek kendinden konak IR _'yi ölçeklendirebilirsiniz_ .

Daha fazla bilgi için bkz.

* [Kopyalama etkinliği performansı iyileştirme özellikleri: şirket içinde barındırılan tümleştirme çalışma zamanı ölçeklenebilirliği](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma ve yapılandırma: ölçek konuları](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>Paralel kopya

`parallelCopies`Kopyalama etkinliğinin kullanmasını istediğiniz paralellik belirtmek için özelliğini ayarlayabilirsiniz. Bu özelliği, kopyalama etkinliğindeki iş parçacığı sayısı üst sınırı olarak düşünün. İş parçacıkları paralel olarak çalışır. İş parçacıkları kaynağınızdan okur ya da havuz veri depolarınızı yazar. [Daha fazla bilgi edinin](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Hazırlanmış kopya

Veri kopyalama işlemi, verileri _doğrudan_ havuz veri deposuna gönderebilir. Alternatif olarak, blob Storage 'ı _geçici bir hazırlama_ deposu olarak kullanmayı da seçebilirsiniz. [Daha fazla bilgi edinin](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Sonraki adımlar

Diğer kopyalama etkinliği makalelerine bakın:

* [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
* [Kopyalama etkinliği performansını sorun giderme](copy-activity-performance-troubleshooting.md)
* [Etkinlik performansını en iyi duruma getirme özelliklerini Kopyala](copy-activity-performance-features.md)
* [Data Lake veya veri ambarınızdan verileri Azure 'a geçirmek için Azure Data Factory kullanın](data-migration-guidance-overview.md)
* [Verileri Amazon S3'ten Azure Depolama'ya geçirme](data-migration-guidance-s3-azure-storage.md)
