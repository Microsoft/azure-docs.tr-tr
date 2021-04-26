---
title: Tümleştirme çalışma zamanı
description: Azure Data Factory'deki tümleştirme çalışma zamanı hakkında bilgi edinin.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: 911674a80b531a50cfb429c5dc0ff41f1aaceb08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389952"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Azure Data Factory'deki tümleştirme çalışma zamanı 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR), Azure Data Factory tarafından farklı ağ ortamlarında aşağıdaki veri tümleştirme özelliklerini sunmak için kullanılan işlem altyapısıdır:

- **Veri akışı**: yönetilen Azure işlem ortamında bir [veri akışı](concepts-data-flow-overview.md) yürütün.  
- **Veri taşıma**: özel ağdaki (Şirket içi veya sanal özel ağ) ortak ağ ve veri depolarında veri depoları arasında veri kopyalama. Yerleşik bağlayıcılar, biçim dönüştürme, sütun eşleme, performanslı ve ölçeklenebilir veri aktarımı desteği sunar.
- **Etkinlik gönderme**: Azure Databricks, Azure hdınsight, Azure Machine Learning, Azure SQL veritabanı, SQL Server ve daha birçok işlem hizmeti üzerinde çalışan dönüştürme etkinliklerini dağıtma ve izleme.
- **SSIS paketi yürütme**: SQL Server Integration Services (SSIS) paketlerini yönetilen bir Azure işlem ortamında yerel olarak yürütün.

Data Factory'de etkinlik, gerçekleştirilecek eylemi tanımlar. Bağlı hizmet, bir hedef veri deposunu veya işlem hizmetini tanımlar. Tümleştirme çalışma zamanı, etkinlik ile bağlı Hizmetler arasında köprü görevi görür.  Bağlı hizmet veya etkinlik tarafından başvurulur ve etkinliğin üzerinde çalıştığı ya da dağıtıldığı işlem ortamını sağlar. Bu şekilde etkinlik hedef veri deposuna veya işlem hizmetine en yakın bölgeden en yüksek performansla gerçekleştirilirken güvenlik ve uyum gereksinimleri korunmuş olur.

Tümleştirme çalışma zamanları, [Yönetim hub](author-management-hub.md) 'ı ve bunlara başvuran etkinlikler, veri kümeleri veya veri akışları aracılığıyla Azure Data Factory UX içinde oluşturulabilir.

## <a name="integration-runtime-types"></a>Tümleştirme çalışma zamanı türleri

Data Factory üç tür Integration Runtime (IR) sunar ve veri tümleştirme özelliklerine ve aradığınız ağ ortamına en uygun türü seçmeniz gerekir.  Bu üç tür şunlardır:

- Azure
- Kendinden konak
- Azure-SSIS

Aşağıdaki tabloda tümleştirme çalışma zamanı türlerinin her birinin sunduğu özellikler ve ağ desteği açıklanmaktadır:

IR türü | Ortak ağ | Özel ağ
------- | -------------- | ---------------
Azure | Veri Akışı<br/>Veri taşıma<br/>Etkinlik dağıtma | Veri Akışı<br/>Veri taşıma<br/>Etkinlik dağıtma
Kendinden konak | Veri taşıma<br/>Etkinlik dağıtma | Veri taşıma<br/>Etkinlik dağıtma
Azure-SSIS | SSIS paketi yürütme | SSIS paketi yürütme


## <a name="azure-integration-runtime"></a>Azure tümleştirme çalışma zamanı

Bir Azure tümleştirme çalışma zamanı şunları yapabilir:

- Azure 'da veri akışları çalıştırma 
- Bulut veri depoları arasında kopyalama etkinliğini Çalıştır
- Aşağıdaki dönüşüm etkinliklerini genel ağa gönderme: Databricks Not defteri/jar/Python etkinliği, HDInsight Hive etkinliği, HDInsight Pig Activity, HDInsight MapReduce etkinliği, HDInsight Spark etkinliği, HDInsight akış etkinliği, Azure Machine Learning Studio (klasik) toplu yürütme etkinliği, Azure Machine Learning Studio (klasik) güncelleştirme kaynak etkinlikleri, saklı yordam etkinliği, Data Lake Analytics U-SQL etkinliği, .NET özel etkinliği, Web etkinliği, arama etkinliği ve meta veri alma etkinliği.

### <a name="azure-ir-network-environment"></a>Azure IR ağ ortamı

Azure Integration Runtime, veri depolarına bağlanmayı ve genel olarak erişilebilen uç noktalarla Hizmetleri hesaplamayı destekler. Yönetilen sanal ağı etkinleştirmek Azure Integration Runtime, özel ağ ortamında özel bağlantı hizmeti kullanılarak veri depolarına bağlanmayı destekler.

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure IR işlem kaynağı ve ölçeklendirme
Azure tümleştirme çalışma zamanı Azure'da tamamen yönetilebilen ve sunucusuz bir işlem sunar.  Altyapı sağlama, yazılım yükleme, düzeltme eki uygulama veya kapasite ölçekleme konusunda endişelenmenize gerek yoktur.  Ayrıca yalnızca gerçekten kullandığınız süre boyunca ödeme yaparsınız.

Azure tümleştirme çalışma zamanı verileri bulut veri depoları arasında güvenli, güvenilir ve yüksek performanslı bir şekilde taşınması için gerekli yerel işlemi sunar.  Kopyalama etkinliğinde kullanılacak veri tümleştirme birimi sayısını belirleyebilirsiniz. Bunu yaptığınızda Azure IR işlem boyutu esnek şekilde ölçeklendirilerek Azure Integration Runtime boyutunu el ile ayarlama ihtiyacını ortadan kaldırır. 

Etkinlik dağıtımı, etkinliği hedef işlem hizmetine yönlendiren hafif bir işlemdir, bu nedenle bu senaryonun işlem boyutunu ölçeklendirmeniz gerekmez.

Azure IR oluşturma ve yapılandırma hakkında daha fazla bilgi için bkz. [Azure Integration Runtime oluşturma ve yapılandırma](create-azure-integration-runtime.md). 

> [!NOTE] 
> Azure Integration Runtime 'da veri akışını çalıştırmak için kullanılacak temel işlem altyapısını tanımlayan veri akışı çalışma zamanı ile ilgili özellikler vardır. 

## <a name="self-hosted-integration-runtime"></a>Kendinden konak tümleştirme çalışma zamanı

Kendinden konak IR şu özelliklere sahiptir:

- Bulut veri depoları ve özel ağdaki veri deposu arasında kopyalama etkinliği çalıştırma.
- Şirket içi veya Azure sanal ağı 'ndaki işlem kaynaklarına karşı aşağıdaki dönüşüm etkinliklerini gönderme: HDInsight Hive etkinliği (BYOC-Kendi kümenizi getir), HDInsight Pig Activity (BYOC), HDInsight MapReduce Activity (BYOC), HDInsight Spark Activity (BYOC), HDInsight akış etkinliği (BYOC), Azure Machine Learning Studio (klasik) toplu yürütme etkinliği, Azure Machine Learning Studio (klasik) güncelleştirme kaynak etkinlikleri, saklı yordam etkinliği, Data Lake Analytics U-SQL etkinliği , Özel etkinlik (Azure Batch çalışır), arama etkinliği ve meta verileri Al etkinliği.

> [!NOTE] 
> Şirket içinde barındırılan tümleştirme çalışma zamanını, SAP HANA, MySQL vb. gibi kendi sürücüsünü getir gerektiren veri depolarını desteklemek için kullanın.  Daha fazla bilgi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE), kendine barındırılan IR 'nin bir bağımlılığı. Lütfen JRE 'nin aynı konakta yüklü olduğundan emin olun.

### <a name="self-hosted-ir-network-environment"></a>Kendinden konak IR ağ ortamı

Genel bulut ortamından doğrudan bir görüş satırı bulunmayan özel bir ağ ortamında güvenli bir şekilde veri tümleştirmesi gerçekleştirmek istiyorsanız, şirket güvenlik duvarınızın arkasındaki şirket içi bir IR veya bir sanal özel ağ içinde şirket içinde barındırılan bir IR yükleyebilirsiniz.  Kendinden konak tümleştirme çalışma zamanı yalnızca açık internete giden HTTP tabanlı bağlantılar oluşturur.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Kendinden konak IR işlem kaynağı ve ölçeklendirme

Şirket içi bir makineye veya özel bir ağ içindeki bir sanal makineye şirket içinde barındırılan IR 'yi yükler. Şu anda kendinden konak IR yalnızca Windows işletim sistemlerinde çalışmaktadır.  

Yüksek kullanılabilirlik ve ölçeklenebilirlik için kendinden konak IR ölçeğini mantıksal örneği birden fazla şirket içi makineyle etkin-etkin modda ilişkilendirerek genişletebilirsiniz.  Daha fazla bilgi için bkz. yönergeler için nasıl yapılır kılavuzlarında [Şirket içinde BARıNDıRıLAN IR oluşturma ve yapılandırma](create-self-hosted-integration-runtime.md) .

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime

Var olan SSIS iş yükünü artırmak ve değiştirmek için Azure-SSIS IR oluşturarak SSIS paketlerini yerel ortamda yürütebilirsiniz.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR ağ ortamı

Azure-SSIS IR ortak ağ veya özel ağ üzerinde sağlanabilir.  Şirket içi verilere erişim için Azure-SSIS IR’nin şirket içi ağınıza bağlı bir Sanal Ağa katılması gerekir.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS IR işlem kaynağı ve ölçeklendirme

Azure-SSIS IR, SSIS paketlerinizi çalıştırmaya ayrılmış Azure sanal makinelerinin tam yönetilen bir kümesidir. SSIS projelerinin/paketlerinin (SSSıSDB) kataloğu için kendi Azure SQL veritabanınızı veya SQL yönetilen örneğinizi getirebilirsiniz. Düğüm boyutunu belirttikten sonra kümedeki düğüm sayısını belirtik ölçeğini genişleterek işlem gücünü artırabilirsiniz. Azure-SSIS Integration Runtime hizmetini gerekli olduğunda durdurup başlatarak çalıştırma maliyetlerini kontrol altına alabilirsiniz.

Daha fazla bilgi için nasıl yapılır kılavuzlarında Azure SSIS IR oluşturma ve yapılandırma makalesine bakın.  Oluşturduktan sonra var olan SSIS paketlerinizi çok az veya sıfır değişiklikle SQL Server Veri Araçları (SSDT) ve SQL Server Management Studio (SSMS) gibi bilinen araçları kullanarak şirket içi SSIS kullanır gibi dağıtabilir ve yönetebilirsiniz.

Azure-SSIS çalışma zamanı hakkında daha fazla bilgi için aşağıdaki makalelere bakın: 

- [Öğretici: SSIS paketlerini Azure’a dağıtma](./tutorial-deploy-ssis-packages-azure.md). Bu makalede, bir Azure-SSIS IR oluşturmak ve SSIS kataloğunu barındırmak için bir Azure SQL veritabanı kullanması için adım adım yönergeler sağlanmaktadır. 
- [Nasıl yapılır: Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md). Bu makale öğreticide genişler ve SQL yönetilen örneği kullanma ve IR 'yi bir sanal ağa katma hakkında yönergeler sağlar. 
- [Azure-SSIS IR’yi izleme](monitor-integration-runtime.md#azure-ssis-integration-runtime). Bu makalede bir Azure-SSIS IR ile ilgili bilgileri ve döndürülen bilgilerdeki durumların açıklamalarını alma işlemi gösterilmektedir. 
- [Azure-SSIS IR’yi yönetme](manage-azure-ssis-integration-runtime.md). Bu makale bir Azure-SSIS IR’yi durdurma, başlatma veya kaldırma işlemini gösterir. Ayrıca, IR’ye daha fazla düğüm ekleyerek Azure-SSIS IR’nizi ölçeklendirmeyi gösterir. 
- [Azure-SSIS IR’yi bir sanal ağa ekleyin](join-azure-ssis-integration-runtime-virtual-network.md). Bu makale Azure-SSIS IR’yi bir Azure sanal ağına ekleme hakkında kavramsal bilgiler sağlar. Ayrıca, Azure portalını kullanarak Azure-SSIS IR’nin sanal ağa katılmasını sağlayacak şekilde sanal ağı yapılandırma adımlarını da sunar. 

## <a name="integration-runtime-location"></a>Tümleştirme çalışma zamanının konumu

### <a name="relationship-between-factory-location-and-ir-location"></a>Fabrika konumu ve IR konumu arasındaki ilişki

Müşteri bir Data Factory örneği oluşturduğunda, veri fabrikasının konumunu belirtmeleri gerekir. Data Factory konumu, veri fabrikası meta verilerinin depolandığı ve işlem hattı tetiklemesinin başlatıldığı konumdur. Fabrika meta verileri yalnızca müşterinin tercih ettiği bölgede depolanır ve diğer bölgelerde depolanmaz.

Bu arada, verileri veri depoları arasında taşımak ve işlem hizmetlerini kullanarak verileri işlemek amacıyla veri fabrikası, başka Azure bölgelerindeki veri depolarına ve işlem hizmetlerine erişebilir. Bu davranış veri uyumluluğu, verimlilik ve düşük ağ kullanım maliyetleri için [global ölçekte kullanılabilen IR](https://azure.microsoft.com/global-infrastructure/services/) aracılığıyla gerçekleştirilir.

IR Konumu arka uç işleminin konumunu tanımlar ve bu veri taşıma, etkinlik dağıtımı ve SSIS paket yürütme işlemlerinin gerçekleştirileceği konumdur. IR konumu veri fabrikasının ait olduğu konumdan farklı olabilir. 

### <a name="azure-ir-location"></a>Azure IR konumu

Azure IR belirli bir konum ayarlayabilirsiniz, bu durumda Etkinlik yürütme veya dağıtım söz konusu bölgede gerçekleşecektir.

Varsayılan olan genel ağda otomatik çözümle Azure IR kullanmayı seçerseniz,

- Kopyalama etkinliği için ADF, havuz veri deposunun konumunu otomatik olarak algılamaya yönelik en iyi çabayı yapar, sonra da varsa aynı bölgede veya aynı coğrafi bölgede bulunan en yakın bir şekilde IR kullanır; Havuz veri deposunun bölgesi algılanamaz ise, Data Factory bölgesindeki IR alternatif olarak kullanılır.

  Örneğin, fabrikanızın Doğu ABD ' de oluşturulduğunu, 
  
  - Batı ABD ' de Azure Blob 'a veri kopyalama sırasında, ADF başarıyla blob 'un Batı ABD olduğunu tespit ederseniz kopyalama etkinliği Batı ABD ' de IR üzerinde yürütülür; bölge algılaması başarısız olursa, kopyalama etkinliği Doğu ABD IR üzerinde yürütülür.
  - Bölgenin algılanamayan verileri Salesforce 'a kopyaladığınızda, kopyalama etkinliği Doğu ABD IR üzerinde yürütülür.

  >[!TIP] 
  >Katı veri uyumluluğu gereksinimleriniz varsa ve verilerin belirli bir coğrafyadan ayrılmamasını sağlamak istiyorsanız, belirli bir bölgede açık bir şekilde Azure IR oluşturabilir ve ConnectVia özelliğini kullanarak Bağlı Hizmeti bu IR’ye yönlendirebilirsiniz. Örneğin, UK Güney blob 'tan UK Güney Azure SYNAPSE Analytics 'e veri kopyalamak istiyorsanız ve verilerin UK ' a ayrılmadığından emin olmak istiyorsanız, UK Güney içinde bir Azure IR oluşturun ve hem bağlı hizmetleri bu IR 'ye bağlayın.

- Arama/GetMetadata/silme etkinliği yürütmesi (ardışık düzen etkinlikleri olarak da bilinir), dönüştürme etkinliği dağıtma (dış etkinlik olarak da bilinir) ve yazma işlemleri (test bağlantısı, klasör listesi ve tablo listesi, Önizleme verileri) için ADF, Data Factory bölgesindeki IR 'yi kullanır.

- ADF, veri akışı için Data Factory bölgesindeki IR 'yi kullanır. 

  > [!TIP] 
  > Veri akışının ilgili veri depolarıyla aynı bölgede (mümkünse) çalıştığından emin olmak iyi bir uygulamadır. Bunu, Azure IR Otomatik Çözümle (veri deposu konumu Data Factory konumuyla aynı ise) veya veri depolarınız ile aynı bölgede yeni bir Azure IR örneği oluşturup veri akışını yürütüşleyerek elde edebilirsiniz. 

Otomatik Çözümle Azure IR için yönetilen sanal ağı etkinleştirirseniz ADF, Data Factory bölgesindeki IR 'yi kullanır. 

Kullanıcı arabirimindeki işlem hattı etkinliğini izleme görünümünde veya etkinlik izleme yükündeki etkinlik yürütme işlemi sırasında kullanıma alınan IR konumunu izleyebilirsiniz.

### <a name="self-hosted-ir-location"></a>Kendinden konak IR konumu

Kendinden konak IR, mantıksal olarak Data Factory'ye kayıtlıdır ve işlevini desteklemek için kullanılan işlem sizin tarafınızdan sağlanır. Bu nedenle kendinden konak IR için açık bir konum özelliği yoktur. 

Kendinden konak IR veri taşıma işlemini gerçekleştirmek için kullanıldığında kaynaktan veri ayıklar ve hedefe yazar.

### <a name="azure-ssis-ir-location"></a>Azure SSIS IR konumu

Ayıklama, dönüştürme, yükleme (ETL) iş akışlarınızda yüksek performansa ulaşmak için doğru Azure-SSIS IR konumunu seçmek önemlidir.

- Azure-SSIS IR konumunun veri fabrikanızın konumuyla aynı olması gerekmez, ancak kendi Azure SQL veritabanınızın veya SSSıSDB 'nin bulunduğu SQL yönetilen örneğinizin konumuyla aynı olmalıdır. Bu şekilde Azure-SSIS Integration Runtime biriminiz farklı konumlar arasında aşırı trafik oluşturmadan kolayca SSISDB öğesine erişebilir.
- Mevcut bir SQL veritabanınız veya SQL yönetilen örneğiniz yoksa, ancak şirket içi veri kaynaklarınız/hedefleri varsa, şirket içi ağınıza bağlı bir sanal ağın bulunduğu konumda yeni bir Azure SQL veritabanı veya SQL yönetilen örneği oluşturmanız gerekir.  Bu şekilde, yeni Azure SQL veritabanı veya SQL yönetilen örneği kullanarak Azure-SSIS IR oluşturabilir ve bu sanal ağı aynı konumda birleştirerek farklı konumlarda veri taşımalarını etkili bir şekilde en aza indirebilirsiniz.
- Mevcut Azure SQL veritabanınızın veya SQL yönetilen örneğinizin konumu, şirket içi ağınıza bağlı bir sanal ağın konumuyla aynı değilse, önce mevcut bir Azure SQL veritabanı veya SQL yönetilen örneği kullanarak Azure-SSIS IR oluşturun ve aynı konumdaki başka bir sanal ağı ve ardından bir sanal ağı farklı konumlar arasında sanal ağ bağlantısı olarak yapılandırın.

Aşağıdaki şemada Data Factory konum ayarları ve tümleştirme çalışma zamanları gösterilmektedir:

![Tümleştirme çalışma zamanının konumu](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Kullanılacak IR'yi belirleme

### <a name="copy-activity"></a>Kopyalama etkinliği

Kopyalama etkinliği için veri akışı yönünü tanımlamak üzere kaynak ve havuz bağlantılı hizmetleri gerektirir. Kopyalama işlemini gerçekleştirmek için kullanılacak olan tümleştirme çalışma zamanı örneğini belirlemek için aşağıdaki mantık kullanılır: 

- **İki bulut veri kaynağı arasında kopyalama**: hem kaynak hem de havuz bağlantılı hizmetler Azure IR KULLANDıĞıNDA, ADF, belirttiğiniz bölgesel Azure IR kullanır ya da Azure IR Otomatik Çözümle 'yi (varsayılan) [tümleştirme çalışma zamanı konumu](#integration-runtime-location) bölümünde açıklandığı şekilde seçin.
- **Bir bulut veri kaynağından özel ağdaki veri kaynağına kopyalama**: Kaynak veya havuz bağlantılı hizmet noktaları kendinden konak IR birimine işaret ediyorsa kopyalama etkinliği kendinden konak Integration Runtime üzerinde yürütülür.
- **Özel ağ üzerindeki iki veri kaynağı arasında kopyalama**: Hem kaynak hem de havuz Bağlantılı Hizmetin aynı tümleştirme çalışma zamanı örneğine işaret etmesi gerekir ve kopyalama Etkinliğini yürütmek için bu tümleştirme çalışma zamanı kullanılır.

### <a name="lookup-and-getmetadata-activity"></a>Lookup ve GetMetadata etkinliği

Lookup ve GetMetadata etkinliği, veri deposu bağlı hizmetiyle ilişkili tümleştirme çalışma zamanı üzerinde yürütülür.

### <a name="external-transformation-activity"></a>Dış dönüşüm etkinliği

Bir dış işlem altyapısını kullanan her dış dönüşüm etkinliğinin, bir tümleştirme çalışma zamanına işaret eden bir hedef işlem bağlı hizmeti vardır. Bu tümleştirme çalışma zamanı örneği, dış el kodlu dönüştürme etkinliğinin dağıtıldığı konumu belirler.

### <a name="data-flow-activity"></a>Veri akışı etkinliği

Veri akışı etkinlikleri, onunla ilişkili Azure tümleştirme çalışma zamanı üzerinde yürütülür. Veri akışları tarafından kullanılan Spark işlem, Azure Integration Runtime veri akışı özellikleri tarafından belirlenir ve ADF tarafından tam olarak yönetilir.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelere bakın:

- [Azure tümleştirme çalışma zamanı oluşturma](create-azure-integration-runtime.md)
- [Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma](create-self-hosted-integration-runtime.md)
- [Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md). Bu makale öğreticide genişler ve SQL yönetilen örneği kullanma ve IR 'yi bir sanal ağa katma hakkında yönergeler sağlar.