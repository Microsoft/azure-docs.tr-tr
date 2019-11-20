---
title: Azure Data Factory'deki tümleştirme çalışma zamanı
description: Azure Data Factory'deki tümleştirme çalışma zamanı hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: abnarain
ms.openlocfilehash: 2b90d95d41bb30226d870a74a47327d6492bbd1e
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122936"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Azure Data Factory'deki tümleştirme çalışma zamanı
Integration Runtime (IR), Azure Data Factory tarafından farklı ağ ortamlarında aşağıdaki veri tümleştirme özelliklerini sunmak için kullanılan işlem altyapısıdır:

- **Veri akışı**: yönetilen Azure işlem ortamında bir [veri akışı](concepts-data-flow-overview.md) yürütün.  
- **Veri taşıma**: özel ağdaki (Şirket içi veya sanal özel ağ) ortak ağ ve veri depolarında veri depoları arasında veri kopyalama. Yerleşik bağlayıcılar, biçim dönüştürme, sütun eşleme, performanslı ve ölçeklenebilir veri aktarımı desteği sunar.
- **Etkinlik gönderme**: Azure Databricks, Azure hdınsight, Azure Machine Learning, Azure SQL veritabanı, SQL Server ve daha birçok işlem hizmeti üzerinde çalışan dönüştürme etkinliklerini dağıtma ve izleme.
- **SSIS paketi yürütme**: SQL Server Integration Services (SSIS) paketlerini yönetilen bir Azure işlem ortamında yerel olarak yürütün.

Data Factory'de etkinlik, gerçekleştirilecek eylemi tanımlar. Bağlı hizmet, bir hedef veri deposunu veya işlem hizmetini tanımlar. Tümleştirme çalışma zamanı, etkinlik ile bağlı Hizmetler arasında köprü görevi görür.  Bağlı hizmet veya etkinlik tarafından başvurulur ve etkinliğin üzerinde çalıştığı ya da dağıtıldığı işlem ortamını sağlar. Bu şekilde etkinlik hedef veri deposuna veya işlem hizmetine en yakın bölgeden en yüksek performansla gerçekleştirilirken güvenlik ve uyum gereksinimleri korunmuş olur.

## <a name="integration-runtime-types"></a>Tümleştirme çalışma zamanı türleri
Data Factory, üç farklı Integration Runtime türü sunar ve ihtiyacınız olan veri tümleştirme ve ağ ortamı özelliklerine uygun türü seçmeniz gerekir.  Bu üç tür şunlardır:

- Azure
- Kendinden konak
- Azure-SSIS

Aşağıdaki tabloda tümleştirme çalışma zamanı türlerinin her birinin sunduğu özellikler ve ağ desteği açıklanmaktadır:

IR türü | Ortak ağ | Özel ağ
------- | -------------- | ---------------
Azure | Veri Akışı<br/>Veri taşıma<br/>Etkinlik dağıtma | &nbsp;
Kendinden konak | Veri taşıma<br/>Etkinlik dağıtma | Veri taşıma<br/>Etkinlik dağıtma
Azure-SSIS | SSIS paketi yürütme | SSIS paketi yürütme

Aşağıdaki şemada gelişmiş veri tümleştirme özellikleri ve ağ desteği sunmak için birlikte kullanılabilecek farklı tümleştirme çalışma zamanları gösterilmiştir:

![Farklı tümleştirme çalışma zamanı türleri](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Azure tümleştirme çalışma zamanı
Azure tümleştirme çalışma zamanları şunları yapabilir:

- Azure 'da veri akışları çalıştırma 
- Bulut veri depoları arasında kopyalama etkinliği gerçekleştirme
- Şu dönüştürme etkinliklerini genel ağa gönderme: Databricks Not defteri/jar/Python etkinliği, HDInsight Hive etkinliği, HDInsight Pig Activity, HDInsight MapReduce etkinliği, HDInsight Spark etkinliği, HDInsight akış etkinliği, makine Toplu yürütme etkinliğini öğrenme, kaynak etkinliklerini Machine Learning güncelleştirme, saklı yordam etkinliği, Data Lake Analytics U-SQL etkinliği, .NET özel etkinliği, Web etkinliği, arama etkinliği ve meta verileri Al etkinliği.

### <a name="azure-ir-network-environment"></a>Azure IR ağ ortamı
Azure Integration Runtime, genel olarak erişilebilen uç noktalarla veri depolarına ve işlem hizmetlerine bağlanmayı destekler. Azure Sanal Ağ ortamı için kendiliğinden konak tümleştirme çalışma zamanı kullanın.

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure IR işlem kaynağı ve ölçeklendirme
Azure tümleştirme çalışma zamanı Azure'da tamamen yönetilebilen ve sunucusuz bir işlem sunar.  Altyapı sağlama, yazılım yükleme, düzeltme eki uygulama ve kapasite ölçeklendirme konularında endişe etmeniz gerekmez.  Ayrıca yalnızca gerçekten kullandığınız süre boyunca ödeme yaparsınız.

Azure tümleştirme çalışma zamanı verileri bulut veri depoları arasında güvenli, güvenilir ve yüksek performanslı bir şekilde taşınması için gerekli yerel işlemi sunar.  Kopyalama etkinliğinde kullanılacak veri tümleştirme birimi sayısını belirleyebilirsiniz. Bunu yaptığınızda Azure IR işlem boyutu esnek şekilde ölçeklendirilerek Azure Integration Runtime boyutunu el ile ayarlama ihtiyacını ortadan kaldırır. 

Etkinlik dağıtma, etkinliği hedef işlem hizmetine yönlendiren basit bir işlemdir. Bu nedenle bu senaryo için işlem boyutu ölçeğini genişletmeniz gerekmez.

Azure IR oluşturma ve yapılandırma hakkında bilgi almak için nasıl yapılır kılavuzlarından Azure IR'yi oluşturma ve yapılandırma bölümüne bakın. 

> [!NOTE] 
> Azure Integration Runtime 'da veri akışını çalıştırmak için kullanılacak temel işlem altyapısını tanımlayan veri akışı çalışma zamanı ile ilgili özellikler vardır. 

## <a name="self-hosted-integration-runtime"></a>Kendinden konak tümleştirme çalışma zamanı
Kendinden konak IR şu özelliklere sahiptir:

- Bulut veri depoları ve özel ağdaki veri deposu arasında kopyalama etkinliği çalıştırma.
- Şirket Içi veya Azure sanal ağı 'ndaki işlem kaynaklarına karşı aşağıdaki dönüştürme etkinliklerini gönderme: HDInsight Hive etkinliği (BYOC-Kendi kümenizi getir), HDInsight Pig Activity (BYOC), HDInsight MapReduce etkinliği (BYOC), HDInsight Spark Etkinlik (BYOC), HDInsight akış etkinliği (BYOC), Machine Learning Batch yürütme etkinliği, Machine Learning güncelleştirme kaynak etkinlikleri, saklı yordam etkinliği, Data Lake Analytics U-SQL etkinliği, özel etkinlik (Azure Batch üzerinde çalışır), arama etkinlik ve meta veri Al etkinliği.

> [!NOTE] 
> Şirket içinde barındırılan tümleştirme çalışma zamanını, SAP HANA, MySQL vb. gibi kendi sürücüsünü getir gerektiren veri depolarını desteklemek için kullanın.  Daha fazla bilgi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE), kendine barındırılan IR 'nin bir bağımlılığı. Lütfen JRE 'nin aynı konakta yüklü olduğundan emin olun.

### <a name="self-hosted-ir-network-environment"></a>Kendinden konak IR ağ ortamı
Ortak bulut ortamından ulaşılamayan özel ağ ortamında güvenli bir şekilde veri tümleştirmesi gerçekleştirmek istiyorsanız kurumsal güvenlik duvarının arkasına veya bir sanal özel ağ içine kendinden konak IR yükleyebilirsiniz.  Kendinden konak tümleştirme çalışma zamanı yalnızca açık internete giden HTTP tabanlı bağlantılar oluşturur.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Kendinden konak IR işlem kaynağı ve ölçeklendirme
Kendinden konak IR'nin şirket içindeki bir makineye veya özel ağ içindeki bir sanal makineye yüklenmesi gerekir. Şu anda kendinden konak IR yalnızca Windows işletim sistemlerinde çalışmaktadır.  

Yüksek kullanılabilirlik ve ölçeklenebilirlik için kendinden konak IR ölçeğini mantıksal örneği birden fazla şirket içi makineyle etkin-etkin modda ilişkilendirerek genişletebilirsiniz.  Daha fazla bilgi için bkz. yönergeler için nasıl yapılır kılavuzlarında [Şirket içinde BARıNDıRıLAN IR oluşturma ve yapılandırma](create-self-hosted-integration-runtime.md) .

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime
Var olan SSIS iş yükünü artırmak ve değiştirmek için Azure-SSIS IR oluşturarak SSIS paketlerini yerel ortamda yürütebilirsiniz.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR ağ ortamı
Azure-SSIS IR ortak ağ veya özel ağ üzerinde sağlanabilir.  Şirket içi verilere erişim için Azure-SSIS IR’nin şirket içi ağınıza bağlı bir Sanal Ağa katılması gerekir.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS IR işlem kaynağı ve ölçeklendirme
Azure-SSIS IR, SSIS paketlerinizi çalıştırmaya ayrılmış Azure sanal makinelerinin tam yönetilen bir kümesidir. Kendisine eklenecek SSIS projelerinin/paketlerinin (SSıSDB) kataloğunu barındırmak için kendi Azure SQL veritabanınızı veya yönetilen örnek sunucunuzu getirebilirsiniz. Düğüm boyutunu belirttikten sonra kümedeki düğüm sayısını belirtik ölçeğini genişleterek işlem gücünü artırabilirsiniz. Azure-SSIS Integration Runtime hizmetini gerekli olduğunda durdurup başlatarak çalıştırma maliyetlerini kontrol altına alabilirsiniz.

Daha fazla bilgi için nasıl yapılır kılavuzlarında Azure SSIS IR oluşturma ve yapılandırma makalesine bakın.  Oluşturduktan sonra var olan SSIS paketlerinizi çok az veya sıfır değişiklikle SQL Server Veri Araçları (SSDT) ve SQL Server Management Studio (SSMS) gibi bilinen araçları kullanarak şirket içi SSIS kullanır gibi dağıtabilir ve yönetebilirsiniz.

Azure-SSIS çalışma zamanı hakkında daha fazla bilgi için aşağıdaki makalelere bakın: 

- [Öğretici: SSIS paketlerini Azure’a dağıtma](tutorial-create-azure-ssis-runtime-portal.md). Bu makale bir Azure-SSIS IR oluşturmaya ilişkin adım adım yönergeler sağlar ve SSIS kataloğunu barındırmak için bir Azure SQL veritabanı kullanır. 
- [Nasıl yapılır: Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md). Bu makale öğreticiye genişleterek Azure SQL veritabanı yönetilen örneği kullanımı ve IR 'yi bir sanal ağa katma hakkında yönergeler sağlar. 
- [Azure-SSIS IR’yi izleme](monitor-integration-runtime.md#azure-ssis-integration-runtime). Bu makalede bir Azure-SSIS IR ile ilgili bilgileri ve döndürülen bilgilerdeki durumların açıklamalarını alma işlemi gösterilmektedir. 
- [Azure-SSIS IR’yi yönetme](manage-azure-ssis-integration-runtime.md). Bu makale bir Azure-SSIS IR’yi durdurma, başlatma veya kaldırma işlemini gösterir. Ayrıca, IR’ye daha fazla düğüm ekleyerek Azure-SSIS IR’nizi ölçeklendirmeyi gösterir. 
- [Azure-SSIS IR’yi bir sanal ağa ekleyin](join-azure-ssis-integration-runtime-virtual-network.md). Bu makale Azure-SSIS IR’yi bir Azure sanal ağına ekleme hakkında kavramsal bilgiler sağlar. Ayrıca, Azure portalını kullanarak Azure-SSIS IR’nin sanal ağa katılmasını sağlayacak şekilde sanal ağı yapılandırma adımlarını da sunar. 

## <a name="integration-runtime-location"></a>Tümleştirme çalışma zamanının konumu
Data Factory konumu, veri fabrikası meta verilerinin depolandığı ve işlem hattı tetiklemesinin başlatıldığı konumdur. Bu arada, verileri veri depoları arasında taşımak ve işlem hizmetlerini kullanarak verileri işlemek amacıyla veri fabrikası, başka Azure bölgelerindeki veri depolarına ve işlem hizmetlerine erişebilir. Bu davranış veri uyumluluğu, verimlilik ve düşük ağ kullanım maliyetleri için [global ölçekte kullanılabilen IR](https://azure.microsoft.com/global-infrastructure/services/) aracılığıyla gerçekleştirilir.

IR Konumu arka uç işleminin konumunu tanımlar ve bu veri taşıma, etkinlik dağıtımı ve SSIS paket yürütme işlemlerinin gerçekleştirileceği konumdur. IR konumu veri fabrikasının ait olduğu konumdan farklı olabilir. 

### <a name="azure-ir-location"></a>Azure IR konumu
Veri taşıma veya etkinlik başlatmanın gerçekleşmesini istediğiniz bölgeyi Azure IR’nin kesin konumu olarak ayarlayabilirsiniz. 

Varsayılan olan **Otomatik çözümle Azure IR** kullanmayı seçerseniz, 

- Kopyalama etkinliğinde ADF, aynı uygun bölgede veya aynı coğrafyadaki en yakın bölgede bulunan en uygun konumu seçmek amacıyla havuzunuzu ve kaynak veri deponuzu otomatik olarak algılamak için veya bunların hiçbiri algılanamıyorsa alternatif olarak veri fabrikası bölgesini kullanmak için özelliklerini en iyi şekilde kullanır.

- Arama/GetMetadata/silme etkinliği yürütmesi (ardışık düzen etkinlikleri olarak da bilinir), dönüştürme etkinliği dağıtma (dış etkinlik olarak da bilinir) ve yazma işlemleri (test bağlantısı, klasör listesi ve tablo listesi, Önizleme verileri), ADF Data Factory bölgesindeki IR kullanır.

- ADF, veri akışı için Data Factory bölgesindeki IR 'yi kullanacaktır. 

  > [!TIP] 
  > Veri akışının ilgili veri depolarıyla aynı bölgede (mümkünse) çalıştığından emin olmak iyi bir uygulamadır. Bunu, Azure IR Otomatik Çözümle (veri deposu konumu Data Factory konumuyla aynı ise) veya veri depolarınız ile aynı bölgede yeni bir Azure IR örneği oluşturup veri akışını yürütüşleyerek elde edebilirsiniz. 

Kullanıcı arabirimindeki işlem hattı etkinliğini izleme görünümünde veya etkinlik izleme yükündeki etkinlik yürütme işlemi sırasında kullanıma alınan IR konumunu izleyebilirsiniz.

>[!TIP]
>Katı veri uyumluluğu gereksinimleriniz varsa ve verilerin belirli bir coğrafyadan ayrılmamasını sağlamak istiyorsanız, belirli bir bölgede açık bir şekilde Azure IR oluşturabilir ve ConnectVia özelliğini kullanarak Bağlı Hizmeti bu IR’ye yönlendirebilirsiniz. Örneğin UK Güney’deki bir Blob’dan UK Güney’deki bir SQL DW’ye veri kopyalamak, fakat verilerin Birleşik Krallık’tan ayrılmamasını sağlamak istiyorsanız UK Güney’de bir Azure IR oluşturup her iki Bağlı Hizmet’i de bu IR’ye yönlendirin.

### <a name="self-hosted-ir-location"></a>Kendinden konak IR konumu
Kendinden konak IR, mantıksal olarak Data Factory'ye kayıtlıdır ve işlevini desteklemek için kullanılan işlem sizin tarafınızdan sağlanır. Bu nedenle kendinden konak IR için açık bir konum özelliği yoktur. 

Kendinden konak IR veri taşıma işlemini gerçekleştirmek için kullanıldığında kaynaktan veri ayıklar ve hedefe yazar.

### <a name="azure-ssis-ir-location"></a>Azure SSIS IR konumu
Ayıklama, dönüştürme, yükleme (ETL) iş akışlarınızda yüksek performansa ulaşmak için doğru Azure-SSIS IR konumunu seçmek önemlidir.

- Azure-SSIS IR konumu, veri fabrikanızın konumuyla aynı olmalıdır, ancak SSıSDB 'nin barındırılması gereken kendi Azure SQL veritabanı/yönetilen örnek sunucunuzun konumuyla aynı olmalıdır. Bu şekilde Azure-SSIS Integration Runtime biriminiz farklı konumlar arasında aşırı trafik oluşturmadan kolayca SSISDB öğesine erişebilir.
- SSıSDB barındırmak için var olan bir Azure SQL veritabanı/yönetilen örnek sunucunuz yoksa ancak şirket içi veri kaynaklarınız/hedefleri varsa, bağlantılı bir sanal ağın bulunduğu konumda yeni bir Azure SQL veritabanı/yönetilen örnek sunucusu oluşturmanız gerekir Şirket içi ağınız.  Bu şekilde, yeni Azure SQL veritabanı/yönetilen örnek sunucusunu kullanarak Azure-SSIS IR oluşturabilir ve bu sanal ağı aynı konumda birleştirerek farklı konumlarda veri taşımalarını etkili bir şekilde en aza indirebilirsiniz.
- SSıSDB 'nin barındırıldığı mevcut Azure SQL veritabanı/yönetilen örnek sunucunuzun konumu, şirket içi ağınıza bağlı bir sanal ağın konumuyla aynı değilse, önce mevcut bir Azure SQL veritabanını kullanarak Azure-SSIS IR oluşturun/ Yönetilen örnek sunucusu ve aynı konumdaki başka bir sanal ağa katılma ve ardından sanal ağı farklı konumlar arasında sanal ağ bağlantısı ile yapılandırma.

Aşağıdaki şemada Data Factory konum ayarları ve tümleştirme çalışma zamanları gösterilmektedir:

![Tümleştirme çalışma zamanının konumu](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Kullanılacak IR'yi belirleme

### <a name="copy-activity"></a>Kopyalama etkinliği

Kopyalama etkinliği için veri akışı yönünü tanımlamak üzere kaynak ve havuz bağlantılı hizmetleri gerektirir. Kopyalama işlemini gerçekleştirmek için kullanılacak olan tümleştirme çalışma zamanı örneğini belirlemek için aşağıdaki mantık kullanılır: 

- **İki bulut veri kaynağı arasında kopyalama**: Hem kaynak hem havuz bağlı hizmetleri Azure IR kullanıyorsa, ADF, bölgesel Azure IR’yi (belirttiyseniz) kullanır veya [Tümleştirme çalışma zamanı konumu](#integration-runtime-location) bölümünde açıklandığı gibi IR’yi otomatik çözümleme (varsayılan) seçeneğini belirlediğinizde Azure IR’nin konumunu otomatik olarak belirler.
- **Bir bulut veri kaynağından özel ağdaki veri kaynağına kopyalama**: Kaynak veya havuz bağlantılı hizmet noktaları kendinden konak IR birimine işaret ediyorsa kopyalama etkinliği kendinden konak Integration Runtime üzerinde yürütülür.
- **Özel ağ üzerindeki iki veri kaynağı arasında kopyalama**: Hem kaynak hem de havuz Bağlantılı Hizmetin aynı tümleştirme çalışma zamanı örneğine işaret etmesi gerekir ve kopyalama Etkinliğini yürütmek için bu tümleştirme çalışma zamanı kullanılır.

### <a name="lookup-and-getmetadata-activity"></a>Lookup ve GetMetadata etkinliği

Lookup ve GetMetadata etkinliği, veri deposu bağlı hizmetiyle ilişkili tümleştirme çalışma zamanı üzerinde yürütülür.

### <a name="transformation-activity"></a>Dönüştürme etkinliği

Her dönüştürme etkinliğinde bir tümleştirme çalışma zamanını işaret eden hedef işlem Bağlı Hizmeti vardır. Bu tümleştirme çalışma zamanı örneği, dönüştürme etkinliğinin dağıtıldığı yerdir.

### <a name="data-flow-activity"></a>Veri akışı etkinliği

Veri akışı etkinliği, onunla ilişkili tümleştirme çalışma zamanı üzerinde yürütülür. 

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Azure tümleştirme çalışma zamanı oluşturma](create-azure-integration-runtime.md)
- [Kendinden konak tümleştirme çalışma zamanı oluşturma](create-self-hosted-integration-runtime.md)
- [Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md). Bu makale öğreticiye genişleterek Azure SQL veritabanı yönetilen örneği kullanımı ve IR 'yi bir sanal ağa katma hakkında yönergeler sağlar. 
